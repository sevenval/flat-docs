# `pass-body` Action

The `pass-body` action sends the unmodified response body of a [`request`](request.md)
to the client. It prevents unnecessary copying which is especially useful when
dealing with binary data.

## Syntax

* `request` (optional): Specifies which request body shall be passed, default `main`.
* `mime` (optional): Sets the `Content-Type`, default `text/plain`.
* `status` (optional): Sets the HTTP response status code, see
[`set-status` action](set-status.md).

## Examples

```xml
<flow>
  <request>{ "url": "…" }</request>
  <pass-body />
</flow>
```

```xml
<flow>
  <request>{ "id": "pass-me", "url": "…" }</request>
  <pass-body request="pass-me" status="200"/>
</flow>
```

## See also

* [Processing Upstream Responses](/cookbook/upstream-response.md)
* [`body()`](/reference/functions/body.md)
