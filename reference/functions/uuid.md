# `uuid3()` and `uuid4()`

```
string uuid3(string namespace, string name)
string uuid4()
```

The functions `uuid3()` and `uuid4()` generate a [UUID](https://tools.ietf.org/html/rfc4122) version 3 or 4, respectively.

## Examples

* `uuid4()` returns `da7e73a7-8dca-4123-b671-7997d7fd2fb8` or a similar, random UUID
* `uuid3('6ba7b810-9dad-11d1-80b4-00c04fd430c8', 'example.com')` returns `9073926b-929f-31c2-abc9-fad77ae3e8eb`.
* `uuid3("foobar")` is not really a call that conforms to the standard, since no namespace (a UUID) was included, but returns `3858f622-30ac-3c91-9f30-0c664312c63f` anyway.

## See also

* [`FIT_FLAT_UID_FORMAT`](/administration/configuration.md#miscellaneous)
