# CORS - Cross-Origin Resource Sharing

[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) is a browser security mechanism to allow Web sites to access data at (i.e. perform HTTP requests to) another _origin_ (proto + domain + port).

In _Single Page Applications_ this is often needed, if the API is running on different domain than the asset domain. For example, if users open `https://example.com` and the Web App fetches from `https://api.example.com`.

It may also be needed in development setups. If the SPA is served by a hot-reloading asset server (such as WebPack or NextJS) on `localhost:3000` and FLAT is running on `localhost:8080`.

## Configuration

CORS handling can be configured in the top-level section of `swagger.yaml` with the extension object `x-flat-cors`:

```yaml
swagger: "2.0"
info:
  description: "…"
  version: "1.0.0"
  title: "My API"
basePath: "/v1"

x-flat-cors:  
  allowed-origins:
    - "https://example.com"
    - "http://localhost:3000"
  allow-credentials: true
```

`allowed-origins` is a mandatory property that defines the origins that are allowed to use this API as an array of strings. Clients send their origin in the `Origin` HTTP request header. If it matches one of the configured origins, it is reflected back to the client in the `Access-Control-Allowed-Origin` header.

If you only want to configure one origin, you can set `allowed-origins` as a string:

```yaml
x-flat-cors:
  allowed-origins: "https://example.com"
```

The special origin `*` allows all Websites to access the API.

The optional property `allow-credentials` controls whether the browser is allowed to send `Cookies` or `Authorization` headers to the API. This is often necessary the API wants to read access tokens from request headers. (Also see [Working with JWT](/cookobook/jwt.md)).

## Swagger Integration

FLAT uses the OpenAPI definition to know which HTTP methods ("operations") are allowed on which paths. This information is sent in response to the `OPTIONS` pre-flight check in the `Access-Control-Allow-Methods: GET` header.

Requests for URLs outside of the API `basePath` are answered with `405 Method not allowed`.

## Example

You can test the example `x-flat-cors` configuration above with `curl` to simulate pre-flight checks from different origins:

```bash
$ curl -i -X OPTIONS -H "Origin: http://localhost:3000" localhost:8080/v1/users
HTTP/1.1 200 OK
Date: Tue, 06 Aug 2019 09:33:19 GMT
Server: FLAT
Access-Control-Allow-Origin: http://localhost:3000
Access-Control-Allow-Credentials: true
Access-Control-Allow-Methods: GET
Content-Length: 0
```

This location is not allowed:

```bash
$ curl -i -X OPTIONS -H "Origin: http://example.com:80" localhost:8080/v1/users
HTTP/1.1 200 OK
Date: Tue, 06 Aug 2019 09:33:19 GMT
Server: FLAT
Access-Control-Allow-Credentials: true
Access-Control-Allow-Methods: GET
Content-Length: 0
```

They look similar, but in the second request the `Access-Control-Allow-Origin` header is missing.


