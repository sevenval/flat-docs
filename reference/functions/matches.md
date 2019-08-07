# `matches()`

```
boolean matches(string input, string pattern [, string modifiers])
```

The `matches` function returns `true()` if `input` matches the regular expression supplied as `pattern` as influenced by
the value of `modifiers`, if present; otherwise, it returns `false()`. Supported modifiers are `i` ("IGNORECASE"), `m` ("MULTILINE"), `s` ("DOTALL") and `x` ("EXTENDED").

Internally, the FIT Server uses [`PCRE`](http://www.pcre.org/original/doc/html/pcresyntax.html).

## Example

`matches("FIT", "^f.", "i")` evaluates to `true()`
