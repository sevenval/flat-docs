# `uuid3()` and `uuid4()`

```
string uuid3(string namespace_and_name)
string uuid4()
```

The functions `uuid3()` and `uuid4()` generate a [UUID](https://tools.ietf.org/html/rfc4122) version 3 or 4, respectively.


## Examples

* `uuid4()` returns `da7e73a7-8dca-4123-b671-7997d7fd2fb8` or a similar, random UUID
* `uuid3("foobar")` is not really a call that conforms to the standard, since no namespace (a binary UUID) was included, but returns `3858f622-30ac-3c91-9f30-0c664312c63f` anyway.
