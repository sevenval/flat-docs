# `proxy-request` Action

The `proxy-request` action forwards the incoming request almost unmodified to an upstream system.

The HTTP method and the request body are taken as-is from the client request.
`Cookie`, `Authorization` and any hop-by-hop header fields like `Connection`
will be dropped automatically, the remaining header fields will be sent upstream.

The response body is written into `fit://request/content/main` where it
can be directly accessed with the [`body`](/reference/functions/body.md) or the
[`content` function](/reference/functions/content.md).
Additional information about the response, such as headers and status code can
be found in the [`$upstream` variable](/reference/variables.md#predefined-variables).

## Usage

Just like an ordinary [`request`](request.md) the `proxy-request`
can be configured using a [JSON template](/reference/templating/README.md)
with the following properties:

### `origin`

Sets the origin of the upstream system. Either `origin` or `url` is required. Type: `string`.

### `stripEndpoint`

To be used in connection with `origin`. If `true`, strips the endpoint path from the client request URL path before it is added to the upstream origin. Type: `boolean`, default: `false`

E.g.

```yaml
basePath: /api
paths:
  /foo/bar:
  /wildcard/**
```

For the client request URL `https://client.example.com/api/foo/bar`, the path is stripped to `/`.

For the client request URL `https://client.example.com/api/wildcard/path/to`, the path is stripped to `/path/to`.

### `addPrefix`

Inserts a path prefix before the given (client request URL) path, after possible endpoint stripping (see `stripEndpoint`). Type: `string`.

### `url`

Sets the URL to the upstream system. Either `url` or `origin` is required.

### `headers`

Sets or removes request header fields. The syntax is the same as in the [request action](request.md#headers).
To remove a header, set its value to `""`.

### `options`

Sets request options. See the [`request` action options](request.md#options) for valid options.

## Examples

Using `origin`:

```xml
<flow>
  <proxy-request>
  {
    "origin": "https://example.com",
	"stripEndpoint": true,
	"addPrefix": "/path/to/api"

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

With the client request URL `http://client.example.com/my/api/foo/bar` matching the swagger definition path

```yaml
basePath: /my/api
paths:
  /foo/**:
    x-flat-flow: proxy.xml
```
the upstream request URL will be `https://example.com/path/to/api/bar`.

Using `url`:

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
