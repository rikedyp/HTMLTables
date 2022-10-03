# HTMLTables
Import HTML tables as APL arrays

```APL
 HTMLTables.GetAll ← {
    ⍵: Simple character vector URL
    ←: Return a nested vector of nested matrices. Each matrix contains data from HTML tables fetched from the URL
}
```

## How to use
1. Import with `]Get` from this repository's URL

```APL
      ]Get https://github.com/aplutils/HTMLTables
#.HTMLTables
```

> Alternatively, clone this repository or [download as .zip](https://github.com/aplutils/HTMLTables/archive/refs/heads/main.zip) and extract
> ```APL
>       ]Get /path/to/HTMLTables
> #.HTMLTables
> ```
> For use in scripts or other applications, import with `⎕SE.Link.Import` as part of a build process
> ```APL
>       'HTMLTables'⎕NS⍬
>       ⎕SE.Link.Import HTMLTables '/path/to/HTMLTables'
>       Imported: #.HTMLTables ← /path/to/HTMLTables
> ```

2. Call `HTMLTables.GetAll` with a URL:

```APL
      5↑4⊃ HTMLTables.GetAll 'https://en.wikipedia.org/wiki/List_of_Wimbledon_gentlemen%27s_singles_champions'
┌──────┬───────┬───────────────┬───────┬───────────────┬─────────────────────────┐
│┌────┐│┌─┬───┐│┌─────────┐    │┌─┬───┐│┌──────────┐   │┌─────────────┐          │
││1968│││ │AUS│││Rod Laver│    ││ │AUS│││Tony Roche│   ││6–3, 6–4, 6–2│          │
│└────┘│└─┴───┘│└─────────┘    │└─┴───┘│└──────────┘   │└─────────────┘          │
├──────┼───────┼───────────────┼───────┼───────────────┼─────────────────────────┤
│┌────┐│┌─┬───┐│┌─────────┐    │┌─┬───┐│┌─────────────┐│┌──────────────────┐     │
││1969│││ │AUS│││Rod Laver│    ││ │AUS│││John Newcombe│││6–4, 5–7, 6–4, 6–4│     │
│└────┘│└─┴───┘│└─────────┘    │└─┴───┘│└─────────────┘│└──────────────────┘     │
├──────┼───────┼───────────────┼───────┼───────────────┼─────────────────────────┤
│┌────┐│┌─┬───┐│┌─────────────┐│┌─┬───┐│┌────────────┐ │┌───────────────────────┐│
││1970│││ │AUS│││John Newcombe│││ │AUS│││Ken Rosewall│ ││5–7, 6–3, 6–2, 3–6, 6–1││
│└────┘│└─┴───┘│└─────────────┘│└─┴───┘│└────────────┘ │└───────────────────────┘│
├──────┼───────┼───────────────┼───────┼───────────────┼─────────────────────────┤
│┌────┐│┌─┬───┐│┌─────────────┐│┌─┬───┐│┌──────────┐   │┌───────────────────────┐│
││1971│││ │AUS│││John Newcombe│││ │USA│││Stan Smith│   ││6–3, 5–7, 2–6, 6–4, 6–4││
│└────┘│└─┴───┘│└─────────────┘│└─┴───┘│└──────────┘   │└───────────────────────┘│
├──────┼───────┼───────────────┼───────┼───────────────┼─────────────────────────┤
│┌────┐│┌─┬───┐│┌──────────┐   │┌─┬───┐│┌────────────┐ │┌───────────────────────┐│
││1972│││ │USA│││Stan Smith│   ││ │ROM│││Ilie Năstase│ ││4–6, 6–3, 6–3, 4–6, 7–5││
│└────┘│└─┴───┘│└──────────┘   │└─┴───┘│└────────────┘ │└───────────────────────┘│
└──────┴───────┴───────────────┴───────┴───────────────┴─────────────────────────┘
```
