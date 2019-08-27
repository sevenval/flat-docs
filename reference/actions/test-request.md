# `test-request` Action

Used in [FLAT tests](/reference/testing/README.md) to call the FLAT app under test.

## Syntax

```xml
<test-request>
{
  "path": "/api/path"
}
</test-request>
```

All [`request`](request.md) parameters and options can be used:

```xml
<test-request>
{
  "path": "/api/path",
  "method": "POST",
  "headers": {
    "X-Foo": "bar"
  },
  "body": {
    "value": {
      "user": "alice"
    }
  }
}
</test-request>
```

The `test-request` sets these variables to inspect the result (with [`assert`](assert.md)):

* `$status` the status code (number)
* `$response` the response body (string)
* `$headers` the response headers (array)

## See also

* [Testing API Requests](/cookbook/test-api-request.md) (cookbook)
* [Testing Upstream Requests](test-backend.md) (cookbook)
* [Testing](/reference/testing/README.md) (reference)