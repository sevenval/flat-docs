# Performing Additional Checks on JWT Access Tokens

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
      claims:
        iss: "The token provider"
      scope-claim: sc # default: scope
      out-var: $jwt
      post-check-flow: check-jwt.xml
paths:
  /projects/{p}:
    x-flat-flow: ...
    get:
      security:
        - JWTCookie: [ read ]
    patch:
      security:
        - JWTCookie: [ write ]
```