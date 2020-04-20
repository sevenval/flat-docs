# Forwarding a Request to an Upstream API

To forward a request to an upstream API ("proxy" a request), you can simply use a
[`proxy-request` action](/reference/actions/proxy-request.md), for example


```xml
<flow>
  <proxy-request>
  {
    "url": "https://httpbin.org/anything"
  }
  </proxy-request>
</flow>
```

to delegate the incoming request to [httpbin.org](https://httpbin.org/#/Anything).

The following [flow](/reference/flow.md) shows a more advanced example:

```xml
<flow>
  <proxy-request>
  {
    {{// Replace the origin of the incoming request with the UPSTREAM_ORIGIN environment variable }}
    "origin": {{ $env/UPSTREAM_ORIGIN }},

    "headers": {
      {{// Set X-tra, drop X-Remove, copy Authorization }}
      "X-tra": "42",
      "X-Remove": "" {{,}}
      {{: $request/headers/authorization }}
    },

    "options": {
      "timeout": "exit-on-error": true,
      "validate-request": true,
      …
    }
  }
  </proxy-request>
</flow>
```

The `proxy-request` action lets you set the `origin` and modify the `headers` of the request.
Everything else is set up automatically: The client request body will be forwarded
as-is, any headers not intended for upstream will be dropped.

If you need to further modify the request, you'll have to configure a
[`request` action](/reference/actions/request.md) that forwards the incoming request
with your modifications upstream.
This gives you full control over the upstream request, but there's quite much thinking
involved to get this fully right.

The following example illustrates, how such a request could look like. We
just modify the `url`, copy `method` and `query`
using [pair producers](/reference/templating/pair-producer.md) and set the request `body`
by referencing the [`$body` variable](/reference/variables.md#predefined-variables):

```xml
<flow>
  <request>
  {
    {{// the URL for the upstream API}}
    "url": {{ replace($request/url, "^http://[^/]+", $env/UPSTREAM_URI) }},

    {{// copy the request method and query }}
    {{: $request/method | $request/query }},

    {{// set the request body}}
    "body": {
      "src": "$body"
    },
    {{// if set, copy specific headers, here foo and bar }}
    "headers": {
      {{: $request/headers/foo | $request/headers/bar }}
    },
    {{// if set, copy specific cookies, here SessionID and quux }}
    "cookies": {
      {{: $request/cookies/SessionID | $request/cookies/quux }}
    }
  }
  </request>

  <set-response-headers>
  {
    {{// pass through the upstream response status (defaults to 200) }}
    "status": {{ $upstream/main/status }}{{,}}
    {{// forward certain upstream response header fields }}
    {{: $upstream/main/headers/cache-control | $upstream/main/headers/set-cookie }}
  }
  </set-response-headers>
</flow>
```

Since we are implicitly using the default request ID `main` in the above example, the body together with
its `Content-Type` is automatically set as the response body to be sent
to the client.

The [`set-response-headers` action](/reference/actions/set-response-headers.md) ensures that the HTTP
status code of the upstream response and possibly additional header fields are correctly forwarded to the client.

The forwarded request will only have a body if the incoming request does.
The `Content-Type` request and response headers are passed automatically with the body, therefore we don't have to copy them explicitly.

If the client and the upstream request URL paths do not share a common prefix, you can easily adjust the path by using `stripEndpoint` and `addPrefix`:

```json
{
  "origin": {{ $env/UPSTREAM_ORIGIN }},
  "stripEndpoint: true,
  "addPrefix": {{ $env/UPSTREAM_PATH_PREFIX }},
  …
}
```

Assuming the following `swagger.yaml` for `https://client.example.com/`

```yaml
basePath: /api
paths:
  /users/**:
    …
```

, `https://users.upstream.example.com` for `UPSTREAM_ORIGIN`, and `/v4` for `UPSTREAM_PATH_PREFIX`, a client request for `https://client.example.com/api/users/profile` will be forwarded to `https://users.upstream.example.com/v4/profile`.


## See also

* [`proxy-request` action](/reference/actions/proxy-request.md) (reference)
* [`request` action](/reference/actions/request.md) (reference)
