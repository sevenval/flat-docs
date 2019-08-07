# `ends-with()`

```
boolean ends-with(string haystack, string needle)
```

The function `ends-with` returns `true` if the first argument string
ends with the second argument string, and otherwise returns `false`.

## Examples

* `ends-with("foo", "oo")` returns `true`
* `ends-with($request/url, ".json")` could be used to identify JSON resources
