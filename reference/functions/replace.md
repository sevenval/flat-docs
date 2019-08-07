# `replace()`

```
string replace(string input, string pattern, string replacement [, string modifiers])
```

The function returns a `string` that is obtained by replacing each non-overlapping substring
of the `input` that matches the given regular expression `pattern` with an occurrence of the `replacement` string,
respecting the optional `modifiers`. Supported modifiers are `i` ("IGNORECASE"), `m` ("MULTILINE"), `s` ("DOTALL") and `x` ("EXTENDED").

Internally, the FIT Server uses [`PCRE`](http://www.pcre.org/original/doc/html/pcresyntax.html).

## Examples

* `replace("FIT", "^F", "H")` returns `HIT`.
* `replace("banAna", "na", "l", "i")` uses the `i` modifier for case-insensitive matching and returns `ball`.

The `replacement` parameter may use the captured sub-patterns from the `pattern` parameter
with `$n` or `${n}`. Every such `$n` will be replaced with the text that has been captured
by the n'th parenthesized sub-pattern or the empty string if less than n sub-expression were
present in the `pattern`:

* `replace("foo-bar-baz", "b(.)(.)", "b$1$1$1${7}$2$2$2")` returns "foo-baaarrr-baaazzz".
