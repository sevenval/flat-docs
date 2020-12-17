# Security

Security-related documentation in Swagger is described in [Security Definitions Object](https://swagger.io/specification/v2/#securityDefinitionsObject) and [Security Requirement Object](https://swagger.io/specification/v2/#securityRequirementObject)

Standard Swagger features in combination with some extensions can be used to configure security checks about JWT tokens which are expected to be sent with a client request.
If the JWT token is valid, it is stored in a [variable](/reference/variables.md), which can then be used in the [flow](/reference/flow.md).

## The `x-flat-jwt` field

The `x-flat-jwt` field references an object with fields describing the expected JWT token:

* `key` - REQUIRED. The key to decode the JSON Web Signature (JWS). This can either be specified with a value, or by referencing a file (`file`) or an environment variable (`env`).
* `alg` - The signing algorithm the JWS is expected to be created with. This can either be specified with a value, or by referencing a file (`file`) or an environment variable (`env`). See the [`algorithm` parameter for `jwt-decode()`](/reference/functions/jwt-decode.md) for more information.
* `out-var` - The name of the variable in which the JWT is stored (must be a proper variable name, starting with `$`; default: `"$jwt"`).
* `out-header` - The name of an HTTP request header that shall carry the JWT in upstream requests.
* `claims` - An object with claims the JWT payload is expected to contain. The field names are the claim names, the expected claim value is specified either with a value, or by referencing a file (`file`) or an environment variable (`env`).
* `scope-claim` - The name of the claim, storing the scope of the token (string; default: `scope`). The claim value must either be
  * a string containing a whitespace-separated list of scopes (`"scope1 scope2 scope3"`) or
  * an array with a string entry for each scope (`["scope1", "scope2", "scope3"]`).
* `post-check-flow` - The path to a flow executed after the JWT is checked and found valid. In this flow, e.g. additional checks can be implemented (use the [`error` action](/reference/actions/error.md) to throw errors).

The token is considered valid if all of the following are true:
* the JWS can be decoded,
* the JWS has a proper JWT,
* the JWT is not expired,
* the JWT contains the expected claims, if any are configured,
* the scope of the token contains all required scopes as specified in the security requirement,
* the JWT can be stored in a variable.

`$jwt` or the alternative variable specified in `out-var` and the header specified in `out-header` will be unset if the token is not valid.

## JWT in `Authorization` Header

Use a [Security Scheme Object](https://swagger.io/specification/v2/#securitySchemeObject) with `type: apiKey`, `in: header` and `name: Authorization` if the JWT is expected to be sent as a bearer token in an `Authorization` header; e.g.:

```yaml
securityDefinitions:
  JWTHeaderAuth:
    type: apiKey
    in: header
    name: Authorization
    x-flat-jwt:
      key:
        file: secret.pem
      alg:
        env: FLAT_JWT_ALG
      out-var: $header_token
      scope-claim: permission
      claims:
        aud:
          env: FLAT_JWT_AUDIENCE
```
The code in this example defines a security scheme named `JWTHeaderAuth`.
The token is expected to be a bearer token in the `Authorization` header.
The key is read from a file named `secret.pem` relative to the `swagger.yaml`.
The signing algorithm is read from the `FLAT_JWT_ALG` environment variable.
The JWT will be stored in the `$header_token` variable.
The scope of the token is specified in a `permission` claim.
The JWT payload is expected to contain an `aud` claim with a value read from the `FLAT_JWT_AUDIENCE` environment variable.

If the request does not contain an `Authorization` header with the proper bearer structure, or the token is invalid, this security scheme will fail.


## JWT in Cookie

Use a [Security Scheme Object](https://swagger.io/specification/v2/#securitySchemeObject) with `type: apiKey`, `in: header`, `name: Cookie` and `x-flat-cookiename: <name>` if the JWT is expected to be sent as a cookie value; e.g.:

```yaml
securityDefinitions:
  JWTCookieAuth:
    type: apiKey
    in: header
    name: Cookie
    x-flat-cookiename: authtoken
    x-flat-jwt:
      key:
        env: FLAT_COOKIE_SECRET
      out-var: $cookie_token
```

In this example the key is read from the `FLAT_COOKIE_SECRET` environment variable.
The JWT signing algorithm is unspecified – it defaults to `HS256`.
The JWT will be stored in `$cookie_token`.
No claims are expected.

If the request does not contain a `Cookie` header with an `authtoken` cookie, or the token is invalid, this security scheme will fail.

## Security Scheme Combinations

Swagger allows for combinations of security schemes to be applied to API endpoints.
If security schemes are specified as _alternatives_, at least one alternative must not fail.
In the following example, a `GET` request to `/foo` must satisfy at least one of the security schemes named `JWTHeaderAuth` and `JWTCookieAuth`.

```yaml
paths:
  /foo:
    get:
      security:
        - JWTHeaderAuth: []
        - JWTCookieAuth: []
```

## Applying Security Schemes

In Swagger, security schemes can be specified at the top level (default security) or for specific operations.
With FLAT, you can also specify a security scheme for a specific path (default security for all operations on the path).

In the following example, a `GET` request to `/foo` must satisfy the security scheme named `JWTHeaderAuth`,
a `POST` request to `/foo` must satisfy the security scheme named `JWTHeaderAuth` and the token must have both `write:foo` and `read:bar` scopes,
while e.g. a `DELETE` or `PUT` request to `/foo` must satisfy the security scheme named `JWTCookieAuth`.
All other requests must satisfy either the `JWTHeaderAuth` **or** `JWTCookieAuth` security schemes.

**Note:** While in Swagger 2.0, specifying scopes in security requirements is limited to OAuth2 security definitions, FLAT has no such limitation.

```yaml
# default
security:
  - JWTHeaderAuth: []
  - JWTCookieAuth: []
paths:
  /foo:
    # default for all operations on /foo
    security:
      - JWTCookieAuth: []
    get:
      # specific for GET on /foo
      security:
        - JWTHeaderAuth: []
    post:
      # specific for POST on /foo
      security:
        - JWTHeaderAuth: [ write:foo, read:bar ]
```

If a request does not pass the security check, it is rejected with status code `403` and error code `3206`, and the [error flow](/reference/OpenAPI/routing.md#error-flow) is run, if configured.

The security check is performed after [validation](validation.md).

## Full Example

```yaml
securityDefinitions:
  JWTHeaderAuth:
    type: apiKey
    in: header
    name: Authorization
    x-flat-jwt:
      key:
        file: secret.pem
      alg:
        env: FLAT_JWT_ALG
      out-var: $header_token
      scope-claim: permission
      claims:
        aud:
          env: FLAT_JWT_AUDIENCE
  JWTCookieAuth:
    type: apiKey
    in: header
    name: Cookie
    x-flat-cookiename: authtoken
    x-flat-jwt:
      key:
        env: FLAT_COOKIE_SECRET
      out-var: $cookie_token
  JWTCookieAuth2:
    type: apiKey
    in: header
    name: Cookie
    x-flat-cookiename: authtoken2
    x-flat-jwt:
      key:
        env: FLAT_COOKIE_SECRET
      out-var: $cookie_token2
      post-check-flow: check-cookie-jwt.xml
security:                     # alternatives: token in Authorization header or authtoken cookie
  - JWTHeaderAuth: []
  - JWTCookieAuth: []
paths:
  /:                          # default security as defined at top-level
  /foo:
    get:
      security:               # token must be in Authorization header
        - JWTHeaderAuth: []
    post:
      # specific for POST on /foo
      security:
        - JWTHeaderAuth: [ write:foo, read:bar ]
  /projects/{p}:
    get:
      security:
        - JWTCookieAuth2: []
      parameters:
        - name: p
          in: path
          type: string
          required: true
```

check-cookie-jwt.xml (checking whether the value of the `pid` JWT claim equals the `p` request path parameter):

```xml
<flow>
  <log>
  {
    "token_id": {{ $cookie_token2/pid }},
    "path_id": {{ $request/params/p }}
  }
  </log>
  <error if="not($cookie_token2/pid) or $cookie_token2/pid != $request/params/p">
  {
    "status": 401,
    "message": "Token is not applicable for this project."
  }
  </error>
</flow>
```

## Forwarding JWT Upstream

The claims of an incoming JWT are stored in `$jwt` – or in the global
[variable](/reference/variables.md) that you specify in the `out-var`
property of `x-flat-jwt`.
In a [`request`](/reference/actions/request.md) or
[`proxy-request`](/reference/actions/proxy-request.md) action you can send
these claims upstream, for example using a query parameter or a header:

```xml
<flow>
  <proxy-request>
  {
    "url": "https://httpbin.org/get",
    "query": {{ $jwt }},                  <!-- ⬅ as query parameters -->
    "headers": {
      "Token": {{ json-stringify($jwt) }} <!-- ⬅ as 'Token' header -->
    }
  }
  </proxy-request>
</flow>
```

If we assume the value of `$jwt` being `{"id":42,"name":"John Doe"}`, the resulting
upstream request URL would be `https://httpbin.org/get?id=42&name=John%20Doe`
and the request would include a `Token` header: `Token: {"id":42,"name":"John Doe"}`.

To just forward the received JWT token upstream as a header, you can also specify
the desired header name with the `out-header` property of `x-flat-jwt`:

```yaml
securityDefinitions:
  JWTHeaderAuth:
    …
    x-flat-jwt:
      key: …
      out-header: Token # ⬅ forwards this JWT in the 'Token' header
```

The `headers` section in `proxy-request` is then no longer needed:

```xml
<flow>
  <proxy-request>
  {
    "url": "https://httpbin.org/get"
  }
  </proxy-request>
</flow>
```

Moreover, if you just want to proxy requests, there's no need to define a [flow](routing.md#assigning-flat-flows)
and you can simply set up your requests using [`x-flat-proxy`](routing.md#assigning-flat-proxies):

```yaml
…
securityDefinitions:
  JWTHeaderAuth:
    …
    x-flat-jwt:
      key: …
      out-header: Token # ⬅ forwards the JWT in the 'Token' header
paths:
  /:
    get:
      x-flat-proxy: # ⬅ no more x-flat-flow
        url: https://httpbin.org/get
      security:
        - JWTHeaderAuth: []
```

## See also

* [Protecting Access using JWT Tokens](/cookbook/x-flat-jwt.md) (cookbook)
