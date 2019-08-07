# `tolower()`

```
string tolower([string|node-set argument])
```

The `tolower` XPath function returns the lowercase equivalent of the Unicode representation of
the argument. If no argument is given, the value of the current context node is used.
As usual, parameters of type [`node-set`](http://www.w3.org/TR/xpath/#node-sets) are automatically
cast to `string`. Parameters of other types, e.g. `number` or `boolean`, are not accepted.
