# Performing Additional Checks on JWT Access Tokens

Before you start, make sure that you read the cookbook [Protecting Access using JWT Tokens](x-flat-jwt.md).

Let's start with the following swagger definition:

swagger.yaml:

```yaml
swagger: "2.0"
host: my-api.com
schemes:
  - https
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
      out-var: $jwt
security:
  - JWTCookie: []
paths:
  /projects/{p}:
    x-flat-flow: …
    get:
      parameters:
        - name: p
          in: path
          description: The project identifier
          type: string
          required: true
    patch:
      parameters:
        - name: p
          in: path
          description: The project identifier
          type: string
          required: true
```

The API has one endpoint with a path parameter `p` indicating the project identifier and two operations (`GET` and `PATCH`). The whole API is secured with a security scheme labelled "JWTCookie".

FLAT will make sure that every request to this endpoint
* has a `Cookie` header
* with a value for the `authtoken` cookie
* that is a JWT
* properly signed and
* not expired.

In addition to this, FLAT provides features for further checks:

* [Claims](#checking-claims)
* [Scopes](#checking-scopes)
* [Post-check flow](#the-post-check-flow)

## Checking Claims

You can e.g. make sure that the token was issued by a certain token provider (`iss` claim)

```yaml
…
    x-flat-jwt:
      key:
        file: pubkey.pem
      alg: RS256
      out-var: $jwt
      claims:
        iss: "https://trustworthy-token-provider.com"  # ⬅ the mandatory value for the iss claim
…
```

and that your API is (one of) the intended audience(s) for the token (`aud` claim)

```yaml
…
    x-flat-jwt:
      key:
        file: pubkey.pem
      alg: RS256
      out-var: $jwt
      claims:
        iss: "https://trustworthy-token-provider.com"
        aud: "https://my-api.com/"    # ⬅ a mandatory value for the aud claim
…
```

A JWT with the following claims will pass the test:

```json
{
  "iss": "https://trustworthy-token-provider.com",
  "aud": [ "https://my-api.com/", "https://a-different-api.org/" ],
  …
}
```

while

```json
{
  "iss": "https://the-reckless-token-provider.com",
  "aud": [ "https://my-api.com/", "https://a-different-api.org/" ],
  …
}
```
or
```json
{
  "iss": "https://trustworthy-token-provider.com",
  "aud": [ "https://a-different-api.org/" ],
  …
}
```
will **not** pass.

## Checking Scopes

Let's restrict the use of the `PATCH` operation to specially authorized requests. We can use scopes to achieve this:

```yaml
…
    patch:
      security:
        - JWTCookie: [ write ]
      parameters:
…
```

FLAT will now look for a scope claim (default claim name is `scope`) with a value of `write`. If the `write` scope is present (perhaps among others, like in `"scope": "read write create"`), the request passes, otherwise it is rejected.

BTW, you can specify another claim name for scopes using the `scope-claim` property of `x-flat-jwt`:

```yaml
…
    x-flat-jwt:
      key:
        file: pubkey.pem
      alg: RS256
      scope-claim: sc    # ⬅ look for scopes in the sc JWT claim
…
```

## The post-check flow

Lastly, we want to check that a certain non-standard JWT claim `pid` matches the path param `p` (the project identifier).

We use the post-check-flow feature:

```yaml
…
    x-flat-jwt:
      key:
        file: pubkey.pem
      …
      post-check-flow: check-jwt.xml
…
```
with check-jwt.xml:
```xml
<flow>
  <!-- $jwt contains the JWT claims, see the out-var property or x-flat-jwt -->
  <log>
  {
    "token_id": {{ $jwt/pid }},
    "path_id": {{ $request/params/p }}
  }
  </log>
  <error if="not($jwt/pid) or $jwt/pid != $request/params/p">
  {
    "status": 401,
    "message": "Token is not applicable for this project."
  }
  </error>
</flow>
```

A JWT with the claim

```json
{
  …
  "pid": "ABC123",
  …
}
```
will give access to `https://my-api.com/projects/ABC123`, but **not** to `https://my-api.com/projects/DEF456`.


## All files together

swagger.yaml:
```yaml
swagger: "2.0"
host: my-api.com
schemes:
  - https
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
      claims:
        iss: "https://trustworthy-token-provider.com"
      scope-claim: sc # default: scope
      out-var: $the_claims
      out-header: JWT
      post-check-flow: check-jwt.xml
security:
  - JWTCookie: []
paths:
  /projects/{p}:
    x-flat-flow: ...
    get:
      parameters:
        - name: p
          in: path
          description: The project identifier
          type: string
          required: true
    patch:
      security:
        - JWTCookie: [ write ]
      parameters:
        - name: p
          in: path
          description: The project identifier
          type: string
          required: true
```

check-jwt.xml:
```xml
<flow>
  <!-- $jwt contains the JWT claims, see the out-var property -->
  <log>
  {
    "token_id": {{ $jwt/pid }},
    "path_id": {{ $request/params/p }}
  }
  </log>
  <error if="not($jwt/pid) or $jwt/pid != $request/params/p">
  {
    "status": 401,
    "message": "Token is not applicable for this project."
  }
  </error>
</flow>
```

## See also

* [FLAT Security](/reference/OpenAPI/security.md) (reference)
