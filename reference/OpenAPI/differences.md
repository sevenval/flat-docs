# Differences from Swagger

FLAT uses an enhanced version of Swagger.

## `x-flat-…` Extensions

First of all, several extensions named `x-flat-…` are recognized on different levels of the definition:

* `x-flat-flow`: [flow](routing.md#assigning-flat-flows) to be started. Recognized at top-level and below `paths`, `paths/<path>` and `paths/<path>/<operation>`.
* `x-flat-init`: [init flow](routing.md#init-flow) (top-level)
* `x-flat-error`: [error flow](routing.md#error-flow) (top-level)
* `x-flat-cors`: [CORS configuration](cors.md) (top-level)
* `x-flat-validate`: [validation](validation.md) (top-level, below `paths/<path>` and `paths/<path>/<operation>`)
* `x-flat-jwt`: [expected JWT](security.md#the-x-flat-jwt-field) (in a [security scheme object](https://swagger.io/specification/v2/#securitySchemeObject))
* `x-flat-cookiename`: [JWT cookie name](security.md#jwt-in-cookie)

## Slimline Definition

Defining endpoints in `swagger.yaml` should be straight-forward. However, typing Swagger compliant YAML or JSON can be somewhat cumbersome. To assign a [flow](routing.md#assigning-flat-flows) to a path with two operations (`get` and `post`) the Swagger specification requires you to write at least:

```yaml
swagger: "2.0"
info:
  title: ""
  version: "1"
paths:
  /login:
    x-flat-flow: login-flow.xml
    get:
      responses:
        default:
          description: ""
    post:
      responses:
        default:
          description: ""
```

In FLAT you may omit the top-level `info` section. And as long as you don't need a schema for [response validation](validation.md), you may even drop the useless `responses` sections:

```yaml
swagger: "2.0"
paths:
  /login:
    x-flat-flow: login-flow.xml
    get:
    post:
```

If you don't care which HTTP methods are allowed for a path, you may skip specifying the operations entirely:

```yaml
swagger: "2.0"
paths:
  /login:
    x-flat-flow: login-flow.xml
```

It is by no means a bad thing to write a conformant definition and provide a `description` or `version` information. However, it frequently slows you down unnecessarily and degrades readability.
FLAT allows you to quickly define the endpoints of your API and start working with them. Once set up, you can gradually refine them by adding more operations and [schemas](validation.md) for requests and responses.

> 📎 Note that [most tools](/cookbook/swagger-docs.md) require a complete Swagger definition and may refuse to process FLAT's slimline definitions.

## Wildcard Paths

While paths ending with `/**` are treated like all other paths in standard Swagger, they are handled specially in FLAT. Such a _wildcard path_ matches any request path having the same prefix and, optionally, arbitrary additional segments. "Normal" paths without a trailing wildcard pattern `/**` are always matched first:

```yaml
swagger: "2.0"
paths:
  /foo:
    # matches /foo

  /foo/bar/**:
    # matches /foo/bar, /foo/bar/…, /foo/bar/…/…, etc., e.g. /foo/bar/1
    # but not e.g. /foo/bar1

  /foo/**:
    # matches /foo/…, /foo/…/…, etc., e.g. /foo/qux

  /**:
    # matches everything else, e.g. /bar
```

The longest matching wildcard path wins. The position of a wildcard path in the definition is irrelevant.

Note that path parameters (i.e. sections enclosed in curly braces) cannot be combined with wildcards.
