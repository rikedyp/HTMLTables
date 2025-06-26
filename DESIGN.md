# APL array to HTML table output
There are some design choices to be made for a function that takes APL matrices as input and returns HTML as output.

## Intermediate representation
I'd like to try and use parent/depth vector representation if possible, at least as an exercise.

## Table sections
Do we care to include `<thead>`, `<tbody>` and `<tfoot>` sections? If so, need to decide how the user specifies these. The intention is to let the array structure dictate the table structure without having to specify anything else if possible.

## Nested tables
Are we supporting nested tables? This seems like an obvious extension, but the details of the overall conversion might dictate how this is implemented.

## Colspan, rowspan
Is there a nice way to represent spanned columns and rows in nested array strutures?

## HTML elements
Do we want any representation for particular content other than character vectors?
