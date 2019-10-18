# `has-class()`

```
boolean has-class(string|node-set class_name [, node-set context])
```

The function `has-class` checks whether an element has a `class` attribute which
contains the given CSS class name. The search is case-insensitive. If no `context` parameter
is given, the current context node is used.

## Examples

`//*[has-class('myClass')]` returns all elements which contain the class name
`myClass` in their `class` attributes.

`has-class('myClass', /*)` returns `true` if the document element contains `myClass` in
its `class` attribute, otherwise `false`.

Searching for multiple class names simultaneously (e.g. `has-class('a b')`) is not supported
and triggers a warning. Use an `and` or an `or` expression instead, e.g.
`has-class('a') and has-class('b')`.

Searching an empty string is also not allowed and triggers a warning.
