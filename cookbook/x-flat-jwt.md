# Protecting Access using JWT Tokens

Imagine you had a [proxy](/reference/OpenAPI/routing.md#assigning-flat-proxies) to an API, e.g. httpbin.org.

swagger.yml:
```yaml
swagger: "2.0" 
basePath: /
paths:
  /httpbin/**:
    x-flat-proxy:
      origin: https://httpbin.org
      stripEndpoint: true
```

Sending a request to FLAT running on localhost port 8080 results in:

```
$ curl -i http://localhost:8080/httpbin/anything
HTTP/1.1 200 OK
…
Content-Type: application/json

{
  "args": {},
  "data": "",
  "files": {},
  "form": {},
  "headers": {
    "Accept": "*/*",
    "Accept-Encoding": "deflate, gzip",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.29.0",
    "X-Amzn-Trace-Id": "…" 
  },
  "json": null,
  "method": "GET",
  "origin": "…",
  "url": "https://httpbin.org/anything" 
}
```

That's what you would expect from httpbin.org, right?

## Restricting access: Swagger Security and `x-flat-jwt`

Now, you don't want anyone except authorized users to use this proxy. This is typically achieved with access tokens. Some tokens are JSON Web Tokens ([JWT](https://tools.ietf.org/html/rfc7519)), while others are opaque.

Swagger has a two-part feature to describe protected access to routes: `securityDefinitions` (what sort of protection is applied …) and `security` (… to which routes), e.g.:

```yaml
swagger: "2.0" 
basePath: /
securityDefinitions:
  JWTCookie:
    type: apiKey
    in: header
    name: Cookie
paths:
  /httpbin/**:
    security:
      - JWTCookie: []
```

This defines a security scheme object (named `JWTCookie`), meaning that some sort of cookie is needed to access certain routes. This is applied to the [wildcard path](/reference/OpenAPI/differences.md#wildcard-paths) `/httpbin/**`.

This documentation feature, with some extensions, is used to make FLAT actually permit access to the route only if a valid JWT token is presented.

First, we define the name of the cookie expected to accompany the API request:

```yaml
…
securityDefinitions:
  JWTCookie:
    type: apiKey
    in: header
    name: Cookie
    x-flat-cookiename: authtoken    # ⬅ specify the cookie name
…
```

Then we specify the [configuration](/reference/OpenAPI/security.md#the-x-flat-jwt-field) for decoding the JWT token:

```yaml
…
  JWTCookie:
    type: apiKey
    in: header
    name: Cookie
    x-flat-cookiename: authtoken
    x-flat-jwt:                     # ⬅ our JWT configuration:
      key:                          # ⬅ the key to decode the JWT …
        file: pubkey.pem            # ⬅ … is read from the file pubkey.pem
      alg: RS256                    # ⬅ the signing algorithm is RS256
…
```

The specified key is a public key, read from the file pubkey.pem, e.g.:
```
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDGSd+sSTss2uOuVJKpumpFAaml
t1CWLMTAZNAabF71Ur0P6u833RhAIjXDSA/QeVitzvqvCZpNtbOJVegaREqLMJqv
FOUkFdLNRP3f9XjYFFvubo09tcjX6oGEREKDqLG2MfZ2Z8LVzuJc6SwZMgVFk/63
rdAOci3W9u3zOSGj4QIDAQAB
-----END PUBLIC KEY-----
```

That's all. Now FLAT will only permit requests if they supply a token that bear an RS256 signature that was created with the private key that matches the given public key.

Usually, you would get the key and algorithm from your identity provider (e.g. an OAuth2 authorization server). That service would be responsible for issuing JWT tokens for your users.

For this tutorial we have prepared a couple of JWT tokens for you to try out different situations:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJzb21lX3VzZXIiLCJpc3MiOiJzb21lX3Byb3ZpZGVyIiwiZXhwIjoxNTkwNDkxNTI4fQ.lJnUpBzMx84_5yigeHeLw4f8sbdSdu_7fWr1--t7EAp8v8K-kSmVYUGnR0Jx4o_ZE84N2M72Kn1pKssrzgTHsFi7txcZHHz_JqgnPgKqsZwjrmWDC-XVvdrSXjAsPO6wn0qy3KEMT1y6Z8YQA4ZyzA1dDsRRIUFiNrgF6_b5pC4
```

```
eybGciOiJSUzI1NiJ9.eyJzdWIiOiJzb21lX3VzZXIiLCJpc3MiOiJzb21lX3Byb3ZpZGVyIn0.bNXv28XmnFBjirPbCzBqyfpqHKo6PpoFORHsQ-80IJLi3IhBh1y0pFR0wm-2hiz_F7PkGQLTsnFiSXxCt1DZvMstbQeklZIh7O3tQGJyCAi-HRVASHKKYqZ_-eqQQhNr8Ex00qqJWD9BsWVJr7Q526Gua7ghcttmVgTYrfSNDzU
```

Let's give it a try:

```
$ curl -i http://localhost:8080/httpbin/anything
HTTP/1.1 403 Forbidden
…
Content-Type: application/json

{"error":{"message":"Security violation","status":403,"requestID":"Xsz@QAv8CEBZfWbmQhKLIQAAAII","info":["JWT Security (JWTCookie): No Cookie header sent"],"code":3206}}
```

Ah, yes, we forgot to present a token in the `authtoken` cookie. But we see, that the protection works.

Let's try again with the first token:

```
$ curl -i -H "Cookie: authtoken=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJzb21lX3VzZXIiLCJpc3MiOiJzb21lX3Byb3ZpZGVyIiwiZXhwIjoxNTkwNDkxNTI4fQ.lJnUpBzMx84_5yigeHeLw4f8sbdSdu_7fWr1--t7EAp8v8K-kSmVYUGnR0Jx4o_ZE84N2M72Kn1pKssrzgTHsFi7txcZHHz_JqgnPgKqsZwjrmWDC-XVvdrSXjAsPO6wn0qy3KEMT1y6Z8YQA4ZyzA1dDsRRIUFiNrgF6_b5pC4" http://localhost:8080/httpbin/anything
HTTP/1.1 403 Forbidden
…
Content-Type: application/json

{"error":{"message":"Security violation","status":403,"requestID":"Xsz80Av8CEBZfWbmQhKLIAAAAIE","info":["JWT Security (JWTCookie): Invalid JWT: Token has expired."],"code":3206}}
```

Hmm, expired. So this one is too old. (Access tokens typically have a restricted period of use.)

OK, let's use the other token:

```
$ curl -i -H "Cookie: authtoken=eybGciOiJSUzI1NiJ9.eyJzdWIiOiJzb21lX3VzZXIiLCJpc3MiOiJzb21lX3Byb3ZpZGVyIn0.bNXv28XmnFBjirPbCzBqyfpqHKo6PpoFORHsQ-80IJLi3IhBh1y0pFR0wm-2hiz_F7PkGQLTsnFiSXxCt1DZvMstbQeklZIh7O3tQGJyCAi-HRVASHKKYqZ_-eqQQhNr8Ex00qqJWD9BsWVJr7Q526Gua7ghcttmVgTYrfSNDzU" http://localhost:8080/httpbin/anything
HTTP/1.1 200 OK
…
Content-Type: application/json

{
  "args": {},
  "data": "",
  "files": {},
  "form": {},
  "headers": {
    "Accept": "*/*",
    "Accept-Encoding": "deflate, gzip",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.29.0",
    "X-Amzn-Trace-Id": "…" 
  },
  "json": null,
  "method": "GET",
  "origin": "…",
  "url": "https://httpbin.org/anything" 
}
```

Tada!

By the way, apart from Cookies, this also works similarly with the `Authorization: Bearer …` header:

```
…
securityDefinitions:
  JWTBearer:
    type: apiKey
    in: header
    name: Authorization             # ⬅
    x-flat-jwt:
      key:
        file: pubkey.pem
      alg: RS256
paths:
  /httpbin/**:
    security:
      - JWTBearer: []
```

You can try that with

```
$ curl -i -H "Authorization: Bearer eybGciOiJSUzI1NiJ9.eyJzdWIiOiJzb21lX3VzZXIiLCJpc3MiOiJzb21lX3Byb3ZpZGVyIn0.bNXv28XmnFBjirPbCzBqyfpqHKo6PpoFORHsQ-80IJLi3IhBh1y0pFR0wm-2hiz_F7PkGQLTsnFiSXxCt1DZvMstbQeklZIh7O3tQGJyCAi-HRVASHKKYqZ_-eqQQhNr8Ex00qqJWD9BsWVJr7Q526Gua7ghcttmVgTYrfSNDzU" http://localhost:8080/httpbin/anything
```

But there are two additional features that can be quite handy: `out-var` and `out-header`.

## Sending JWT claims upstream: `out-header`

With [`out-header`](/reference/OpenAPI/security.md#forwarding-jwt-upstream) you can send the whole set of claims from the JWT upstream:

```yaml:
…
    x-flat-jwt:
      key:
        file: pubkey.pem
      alg: RS256
      out-var: $the_claims
      out-header: JWT               # ⬅ the name of the request header with the JWT claims
…
```

```
$ curl -i -H "Cookie: authtoken=eybGciOiJSUzI1NiJ9.eyJzdWIiOiJzb21lX3VzZXIiLCJpc3MiOiJzb21lX3Byb3ZpZGVyIn0.bNXv28XmnFBjirPbCzBqyfpqHKo6PpoFORHsQ-80IJLi3IhBh1y0pFR0wm-2hiz_F7PkGQLTsnFiSXxCt1DZvMstbQeklZIh7O3tQGJyCAi-HRVASHKKYqZ_-eqQQhNr8Ex00qqJWD9BsWVJr7Q526Gua7ghcttmVgTYrfSNDzU" http://localhost:8080/httpbin/anything
HTTP/1.1 200 OK
…
Content-Type: application/json

{
…
  "headers": {
…
    "Jwt": "{\"sub\":\"some_user\",\"iss\":\"some_provider\"}",
…
  },
…
}
```

## Storing JWT claims: `out-var`

With `out-var` you can specify the name of a [variable](/reference/variables.md) where FLAT will store the JSON claims encoded in the JWT, in order to make them available for further processing. E.g.


```yaml
…
    x-flat-jwt:
      key:
        file: pubkey.pem
      alg: RS256
      out-var: $the_claims          # ⬅
…
```

We can log the claims by adding a [`log` action](/reference/actions/log.md) to an [init flow](/reference/OpenAPI/routing.md#init-flow):

```yaml
swagger: "2.0" 
…
x-flat-init: init.xml
paths:
…
```

with init.xml:

```xml
<flow>
  <log>
  {
    "JWT-Claims": {{ $the_claims }}
  }
  </log>
</flow>
```

If you look at the [FLAT logs](/administration/logging.md) and try again with a valid token, you'll notice:

```
{…,"type":"flat_access",…,"JWT-Claims":{"sub":"some_user","iss":"some_provider"}}
```

Here you see the two claims from the JWT token.

## All files together

swagger.yaml:
```yaml
swagger: "2.0" 
basePath: /
securityDefinitions:
  JWTCookie:
    type: apiKey
    in: header
    name: Cookie
    x-flat-cookiename: authtoken
    x-flat-jwt:
      key:
        file: pubkey.pem
      alg: RS256
      out-var: $the_claims
      out-header: JWT
x-flat-init: init.xml
paths:
  /httpbin/**:
    security:
      - JWTCookie: []
    x-flat-proxy:
      origin: https://httpbin.org
      stripEndpoint: true
```

pubkey.pem:
```
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDGSd+sSTss2uOuVJKpumpFAaml
t1CWLMTAZNAabF71Ur0P6u833RhAIjXDSA/QeVitzvqvCZpNtbOJVegaREqLMJqv
FOUkFdLNRP3f9XjYFFvubo09tcjX6oGEREKDqLG2MfZ2Z8LVzuJc6SwZMgVFk/63
rdAOci3W9u3zOSGj4QIDAQAB
-----END PUBLIC KEY-----
```

init.xml:
```xml
<flow>
  <log>
  {
    "JWT-Claims": {{ $the_claims }}
  }
  </log>
</flow>
```

## See also

* [FLAT Security](/reference/OpenAPI/security.md) (reference)
* [Routing: Init Flow and FLAT Proxies](/reference/OpenAPI/routing.md) (reference)
* [Encoding and Decoding JWT](jwt.md) (cookbook)
