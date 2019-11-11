# `urldecode()`, `url- decode()`

```
string urldecode(string argument)
string url-decode(string argument)
```

`urldecode` expects exactly one `string` as a parameter. Returns a string in which all sequences with percent (%) signs followed by two hex digits have been replaced by the corresponding literal characters.
`url-decode` is an alias for `urldecode`.
