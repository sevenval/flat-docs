# `urlencode()`

```
string urlencode(string argument)
```

`urlencode` expects exactly one `string` as a parameter. Returns a string in which all non-alphanumeric characters except -_.~ have been replaced with a percent (%) sign followed by two hex digits. Cf. [RFC 3986](http://www.faqs.org/rfcs/rfc3986.html)
