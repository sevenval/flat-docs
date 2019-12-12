# Differences from Swagger

FLAT uses an adapted version Swagger.

## `x-flat-…` Extensions

First of all, several extensions named `x-flat-…` are recognized on different levels of the definition:

* `x-flat-flow`: [flow](routing.md#assigning-flat-flows) to be started. Recognized at top-level and below `paths`, `paths/<path>` and `paths/<path>/<operation>`.
* `x-flat-init`: [init flow](routing.md#init-flow) (top-level)
* `x-flat-error`: [error flow](routing.md#error-flow) (top-level)
* x-flat-cors: [CORS configuration](cors.md) (top-level)
* x-flat-validate: [validation](validation.md) (top-level)

## Slimline Definition

Defining endpoints in `swagger.yaml` should be straight-forward. However, typing Swagger compliant YAML or JSON can be rather cumbersome. To assign a [flow](routing.md#assigning-flat-flows) to a path with two operations (`get` and `post`) the Swagger specification requires you to write at least:

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

If you don't care which HTTP methods are allowed for a path, you may even go without the concrete operations `get` and `post`:

```yaml
swagger: "2.0"
paths:
  /login:
    x-flat-flow: login-flow.xml
```

It is by no means a bad thing to write a conformant definition and provide a `description` or `version` information. But often it slows you unnecessarily down and worsens readability.
FLAT's slimline syntax allows you to quickly define the endpoints of your API and start working on them. Once setup, you can gradually elaborate them by adding more operations and [schemas](validation.md) for requests and responses.

> 📎 Note that [other tools](/cookbook/swagger-docs.md) often require a complete Swagger definition and may refuse to process FLAT's slimeline defintions.

## Wildcard Paths

While paths ending with `/**` are not treated specially in standard Swagger, they are handled specifically in FLAT. Such a _wildcard path_ matches any request path having the same prefix and arbitrary following segments. "Normal" paths without a trailing wildcard pattern `/**` are always matched first:

```yaml
swagger: "2.0"
paths:
  /foo:
    # matches /foo

  /foo/bar/**:
    # matches /foo/bar, /foo/bar/…, /foo/bar/…/…, etc., e.g. /foo/bar/1

  /foo/**:
    # matches /foo/…, /foo/…/…, etc., e.g. /foo/qux

  /**:
    # matches everything else, e.g. /bar
```
