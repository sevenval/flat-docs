# `body()`

```
string body([string request-id])

```

The `body` function provides access to an upstream response body. The result is
the raw body – if needed, parsing has to be done explictly.

When called without parameter, the function returns the body for the request
with the ID `main`.


## Example

```xml
<flow>
  <request>
    {"id": "json", "url": "https://example.com/json"}
  </request>
  <request>
    {"id": "xml", "url": "https://example.com/xml"}
  </request>

  <eval out="$json">json-parse(body('json'))</eval>
  <eval out="$dom">xml-parse(body('xml'))</eval>
  …
</flow>
```

## See also

* [Processing Upstream Responses](/cookbook/upstream-response.md)
* [`pass-body` action](/reference/actions/pass-body.md)
