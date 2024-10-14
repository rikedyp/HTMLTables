# Notes on performance
To start with, we have relied on both 3rd party utilities and nested array techniques. In this document we will discuss potential performance improvements and refinements to the code.

## Why not use html2xhtml?
There is a decently performant and freely available tool, called [html2xhtml](https://www.it.uc3m.es/jaf/html2xhtml/), for correcting most common HTML issues that prevent pages from being parsed by `⎕XML`. Currently there are difficulties with neatly distributing a native executable dependency.

- `⎕CMD` and `⎕SH` do not allow sending APL data directly to shell processes
    while this might be alleviated by `⎕SHELL`, it is still technically a security risk and not exactly best practice
- I have not put time into wrapping html2xhtml as a `⎕NA`-able library
- html2xhtml is GPL-2 licensed, which could be an issue for potential users
    this could be worked around by making the dependency on html2xhtml a "plugin" which is provided separately from HTMLTables under a different license.

## Closing unclosed tags
The most common issue, even with well formed HTML, is the presence of lone open tags which make pages invalid XML.

I have been slowly refining some code to locate unclosed open tags and close them. Note that this is very different from the types of corrections that html2xhtml does. However, since we are only interested in the pure text content of table cells and not the semantic structure of the HTML, this is a fine and highly performant approach for our purposes.

### Finding a simple character sequence
The "find" primitive function (`⍺⍷⍵`) is used to locate subsequences. However, for very small sequences and large texts, it can be faster to use direct comparison of characters.

```
      ]runtime -c "'</'⍷arg" "∧⌿0 1⌽'</'∘.=arg" "('<'=arg)∧1⌽'/'=arg"
                                                                               
  '</'⍷arg            → 1.1E¯4 |   0% ⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕ 
  ∧⌿0 1⌽'</'∘.=arg    → 7.8E¯6 | -93% ⎕⎕⎕                                      
  ('<'=arg)∧1⌽'/'=arg → 5.7E¯6 | -95% ⎕⎕  
```

### Don't Repeat Yourself (DRY)
In a small experiment, it is surprising to find that the difference due to recomputing some values is negligable - at least for this size of input (`32949=≢t`).

```
      ]runtime -c "##.CloseUnclosedTags t" "##.CloseUnclosedTags2 t"
                                                                                  
  ##.CloseUnclosedTags t  → 2.7E¯3 |  0% ⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕ 
  ##.CloseUnclosedTags2 t → 2.5E¯3 | -5% ⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕   
```

Tried again with the full page input (`1227754=≢full`).

```
      ]runtime -c "##.CloseUnclosedTags full" "##.CloseUnclosedTags2 full"
                                                                                     
  ##.CloseUnclosedTags full  → 1.2E¯1 |  0% ⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕ 
  ##.CloseUnclosedTags2 full → 1.1E¯1 | -6% ⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕⎕
```

```
 CloseUnclosedTags←{
     o←('<'=⍵)>'/'=1⌽⍵     ⍝ open tags are < not followed by /
     c←('<'=⍵)∧1⌽('/'=⍵)   ⍝ closed tags marked by </
     t←¯1(⊢∧⌽)≠\o∨(-≢⍵)↑∊<\¨o⊂⍵∊' >'                             ⍝ open tag type=1
     t-←2×'/'(⊢∨=)(t=1)∧¯1(⊢∨⌽)≠\∊sc×2=+/¨sc←(o⊂o)∨<\¨o⊂'/>'⍷⍵   ⍝ ignore self-closing tags
     t+←2×¯1(⊢∧⌽)⍣2≠\c∨(-≢⍵)↑∊<\¨c⊂⍵∊' >'                        ⍝ close tag type=2
     ot←(t=1)⊆⍵                    ⍝ open tags
     ct←(t=2)⊆⍵                    ⍝ close tags
     PDE←{⍺(R⍨∊R←≢⍤⊢⍴∘⍋∘⍋⍺⍳⍪⍨)⍵}   ⍝ APLCart: Progressive membership without replacement
     ut←~ot PDE ct                 ⍝ each occurance of a close tag consumes an occurance of the corresponding open tag. Unclosed tags are the remaining open tags.
     otm←(-≢⍵)↑¯1(⊢∧⌽)≠\∊(⊂⍨o)∨<\¨o⊂⍵='>'                  ⍝ mark entire open tag
     '/'@((¯1+⍳+/x)+⍸x)⊢⍵⌿⍨1+x←2>/0,otm∧(+\2</0,t=1)∊⍸ut   ⍝ close unclosed tags
 }
```

```
 CloseUnclosedTags2←{
     s←'/'=⍵
     lt←'<'=⍵
     o←lt>1⌽s   ⍝ open tags are < not followed by /
     c←lt∧1⌽s   ⍝ closed tags marked by </
     t←¯1(⊢∧⌽)≠\o∨(-≢⍵)↑∊<\¨o⊂⍵∊' >'                             ⍝ open tag type=1
     t-←2×s∨(t=1)∧¯1(⊢∨⌽)≠\∊sc×2=+/¨sc←(o⊂o)∨<\¨o⊂s∧1⌽gt←'>'=⍵   ⍝ ignore self-closing tags
     t+←2×¯1(⊢∧⌽)⍣2≠\c∨(-≢⍵)↑∊<\¨c⊂gt∨' '=⍵                      ⍝ close tag type=2
     ot←(t=1)⊆⍵                    ⍝ open tags
     ct←(t=2)⊆⍵                    ⍝ close tags
     PDE←{⍺(R⍨∊R←≢⍤⊢⍴∘⍋∘⍋⍺⍳⍪⍨)⍵}   ⍝ APLCart: Progressive membership without replacement
     ut←~ot PDE ct                 ⍝ each occurance of a close tag consumes an occurance of the corresponding open tag. Unclosed tags are the remaining open tags.
     otm←(-≢⍵)↑¯1(⊢∧⌽)≠\∊(o⊂o)∨<\¨o⊂gt                     ⍝ mark entire open tag
     '/'@((¯1+⍳+/x)+⍸x)⊢⍵⌿⍨1+x←2>/0,otm∧(+\2</0,t=1)∊⍸ut   ⍝ close unclosed tags
 }
```

### Operations on partitioned or ragged data
The way we locate open, close and self-close tags is by partitioning the input and locating certain features within each partition.

For example, `sc←(o⊂o)∨<\¨o⊂'/>'⍷⍵` is a less-than scan over partitions. We will investigate using flat array techniques for equivalent computations.
