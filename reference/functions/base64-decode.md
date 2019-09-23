# `base64-decode()`

```xml
string base64-decode(string input [, boolean strict])
```

Decodes the Base64 encoded data given in the `input` parameter.

If the optional `strict` parameter equals `true()`, the
function will return `false()` if the input contains characters that are not
part of the Base64 alphabet. Otherwise invalid characters will be silently dropped.
