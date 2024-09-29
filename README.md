# HTMLTables
Import HTML tables as APL arrays

```APL
 HTMLTables.FromURL ← {
    ⍵: Simple character vector URL
  {⍺}: Indices of tables to import (default all ⍳≢tables)
    ←: Return a nested vector of nested matrices. Each matrix contains data from HTML tables fetched from the URL
}
```

## How to use

### Import Tatin package

HTMLTables is (not yet) only available as a Tatin package.

```
]Tatin.LoadPackages RikedyP-HTMLTables
```

### Call `HTMLTables.FromURL` with a URL:

```
      ⍴r←HTMLTables.FromURL'https://aplwiki.com/wiki/Dyalog_APL'
5
      5↑2⊃r
┌──────┬────┬───────┬───────────────────────────────────────────────────────────────────────────────────┐
│Number│Year│Month  │Features                                                                           │
├──────┼────┼───────┼───────────────────────────────────────────────────────────────────────────────────┤
│1     │1983│April  │(Zilog S8000 only)                                                                 │
├──────┼────┼───────┼───────────────────────────────────────────────────────────────────────────────────┤
│2     │1984│       │(Many more platforms)                                                              │
├──────┼────┼───────┼───────────────────────────────────────────────────────────────────────────────────┤
│3.0   │1985│       │(More platforms) Rectangular display of arrays                                     │
├──────┼────┼───────┼───────────────────────────────────────────────────────────────────────────────────┤
│4.0   │1986│October│User-defined operators,function assignment(including forderived functions),⎕MONITOR│
└──────┴────┴───────┴───────────────────────────────────────────────────────────────────────────────────┘
```
