# Passing Header Fields to the Client

To add arbitrary header fields sent by the upstream system to the client response
use the [`set-response-headers` action](../reference/actions/set-response-headers.md) together with
[pair producers](../reference/templating/pair-producer.md) to copy the wanted header fields of the incoming upstream response (`$upstream/<request ID>/headers`).
Here we pass `Content-Type` and `Cache-Control`:

```xml
<flow>
  <request content="httpbin">{ "url": "http://httpbin.org/cache/600" }</request>

  <set-response-headers>
  {
    {{: $upstream/httpbin/headers/content-type }}
    {{: $upstream/httpbin/headers/cache-control }}
  }
  </set-response-headers>
</flow>
```

Both expressions can be combined to

```xml
  {
    {{: $upstream/httpbin/headers/content-type | $upstream/httpbin/headers/cache-control }}
  }
```

which in turn may be shortened using [`with`](../reference/templating/with.md):

```xml
  {
    {{with $upstream/httpbin/headers }} {{: content-type | cache-control }} {{end}}
  }
```
