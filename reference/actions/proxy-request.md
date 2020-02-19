# `proxy-request` Action

The `proxy-request` action forwards the incoming request almost unmodified to an upstream system.

The HTTP method and the request body are taken as-is from the client request.
`Cookie`, `Authorization` and any hop-by-hop header fields like `Connection`
will be dropped automatically, the remaining header fields will be sent upstream.

The response body is written into `fit://request/content/main` where it
can be directly accessed with the [`content()` function](/reference/functions/content.md).
Additional information about the response, such as headers and status code can
be found in the [`$upstream` variable](/reference/variables.md#predefined-variables).

## Usage

Just like an ordinary [`request`](request.md) the `proxy-request`
can be configured using a [JSON template](/reference/templating/README.md)
with the following properties:

### `url`

Sets the URL to the upstream system. Required.

### `headers`

Sets or removes request header fields. The syntax is the same as in the [request action](request.md#headers).
To remove a header, set its value to `""`.

### `options`

Sets request options. See the [`request` action options](request.md#options) for valid options.

## Example

```xml
<flow>
  <proxy-request>
  {
    "url": "https://example.com/api/",

    "headers": {
      "X-API-Key": "foo42bar"
    },

    "options": {
      "exit-on-error": true,
      "definition": "upstream.yaml",
      "validate-request": true,
      "validate-response": true
    }
  }
  </proxy-request>
</flow>
```

## See also

* [Forwarding a Request to an Upstream API](/cookbook/forward-request-upstream.md)
* [`request` action](/reference/actions/request.md) (reference)
