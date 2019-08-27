# `backend-flow` Action

Used in [FLAT tests](/reference/testing/README.md) to _register_ a flow that mocks an upstream response.

## Syntax

```xml
<backend-flow request="id">
  <!-- flow actions -->
</backend-flow>
```

When [upstream requests](/reference/actions/request.md) are carried out, their _request IDs_ are matched against the `request=` attribute of the backend flow. On match, the backend flow is executed instead of the actual HTTP request.

If at least one backend flow is registered, no HTTP request will be sent. If no backend flow matches, the request will fail.

## See also

* [Testing API Requests](/cookbook/test-api-request.md) (cookbook)
* [Testing Upstream Requests](/cookbook/test-backend.md) (cookbook)
* [Testing](/reference/testing/README.md) (reference)
