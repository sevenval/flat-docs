# `body()`

```
string body([string request-id])

```

The `body` function provides access to an upstream response body. The result is
the raw body – if needed, parsing has to be done explictly.

When called without a parameter, the function returns the body for the request
with the ID `main`, which is the default ID for an [upstream request](/reference/actions/request.md).


## Example

```xml
<flow>
  <request>
    {"url": "https://example.com/json"}
  </request>
  <request>
    {"id": "xml", "url": "https://example.com/xml"}
  </request>

  <eval out="$json">json-parse(body())</eval>
  <eval out="$dom">xml-parse(body('xml'))</eval>
  …
</flow>
```

## See also

* [Processing Upstream Responses](/cookbook/upstream-response.md)
* [`pass-body` action](/reference/actions/pass-body.md)
* [`request` action](/reference/actions/request.md)
