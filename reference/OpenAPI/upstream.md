# Upstream APIs

Typically, FLAT uses one or more upstream APIs where it forwards requests to.

## Validation

You can use Swagger to validate the use of upstream APIs, too!

The [tutorial](/tutorial/README.md) has a section on [upstream validation](/tutorial/README.md#upstream-validation).

### Configuration

The configuration takes place in the [`request` action](/reference/actions/request.md) with this [options](/reference/actions/request.md#options):

* `definition` - The path to the swagger definition file (type: `string`)
* `validate-request` - Whether to validate the request (valid values: `true`, `false`, "report-only", default: `false`)
* `validate-response` - Whether to validate the response (valid values: `true`, `false`, "report-only", default: `false`)

### Error Handling

`validate-request` checks that the HTTP request that is about to be sent adheres to the upstream API schema. If it doesn't, the request _will not be sent_. The response will be an [error document](validation.md#system-error-document) with status `400 Bad Request`.

```json
{
  "error": {
    "message": "Upstream Request Validation Failed",
    "error": 3202,
    "status": 400,
    "requestID": "W@W8DrjPEMPxyqu4zAL4PAAAABg",
    "info": [
       "Invalid method post."
     ]
  }
}
```

If `validate-response` detects that the response body violates the schema, the response will be replaced by a system error.

```json
{
  "error": {
    "message": "Upstream Response Validation Failed",
    "error": 3203,
    "status": 502,
    "requestID": "W@W8DrjPEMPxyqu4zAL4PAAAABg",
    "info": [
       "Type constraint violated in body for error: Object value found, but a string is required.",
       "Upstream status: 404 Not Found"
     ]
  }
}
```

The informational message (array) also contains the original status code of the upstream response – in this case a `404` that may explain the problem.


In both cases, the error will be noted in `$upstream/id/error` where `id` is the content ID of the request (defaults to `main`).

If the request was configured with the [`exit-on-error` option](/reference/actions/request.md#options), errors (e.g. connection errors, invalid requests or responses) cause the normal flow to be aborted. If configured, the [error flow](/reference/OpenAPI/routing.md#error-flow) is run. Otherwise a standard error message is substituted as the response to the client request. See [more on error flows](/cookbook/error-flow.md) in the cookbook.


## Mocking

You can mock upstream responses, too. This is handy for writing tests or developing against upstream APIs that do not (yet) exist.

### Configuration

As above, we use request options:

* `definition` - The path to the swagger definition file (type: `string`)
* `mock-response` - Whether to mock the response (type: `boolean`, default: `false`)

It works just as [client response mocking](mocking.md). The mocked response will be used as the HTTP response body for that `request`.

Usually, the `mock` option is set dynamically:

```xml
<flow>
  <request>
  {
      "url": "https://example.com/",
      "options": {
          "definition": "upstream.yaml" {{,}}
          {{if $request/get/mock }}
            "mock-response": true
          {{end}}
      }
  }
  </request>

  <!-- process response -->
</flow>
```

If called with the query param `mock`, the example response of `upstream.yaml` is used:

```bash
$ curl localhost:8080/v1/…?mock
```
