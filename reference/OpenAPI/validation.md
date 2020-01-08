# Validation

OpenAPI has rich tools to describe inbound [parameters](https://swagger.io/docs/specification/2-0/describing-parameters/), the [request body](https://swagger.io/docs/specification/2-0/describing-request-body/) and [API responses](https://swagger.io/docs/specification/2-0/describing-responses/). FLAT can validate all incoming and outgoing HTTP messages against these schemas.

Code and document tend to divert quickly. Therefore, it is strongly recommended to activate validation. It helps spotting errors early during development. In production, the error logs help noticing and diagnosing validation errors.

The [tutorial](/tutorial/README.md) has a chapter on [validation](/tutorial/README.md#request-validation).

## Configuration

Overall validation is configured in the top-level [extension object `x-flat-validate`](differences.md#x-flat-extensions):

```yaml
swagger: "2.0"
info:
  description: "…"
  version: "1.0.0"
  title: "My API"
basePath: "/v1"

x-flat-validate:
  request: true
  response: true
```

The `request` property activates validation of inbound parameters and request bodies. If the path operation is not defined or the HTTP message does not adhere to the schema, the request is terminated with a [system error](#system-error-document) and status `400`. In that case, the [configured flow](routing.md) is _not_ executed.

The `response` property activates validation of the outgoing HTTP response before it is sent to the client. If the response status code is not defined for the path or the response body does not follow the schema, the request is terminated with a [system error](#system-error-document) and status `500`.

Whenever a schema error has been detected, a meaningful message is logged.

Possible values for the validation properties are:

* `true` activates validation and enforces the schema
* `false` disables Swagger validation (however, the paths must still be defined)
* `report-only` performs validation and logs a validation error without terminating the request

The default value is `false`.

You can also set `x-flat-validate` more specifically for certain paths and methods, for example:

```yaml
swagger: "2.0"
x-flat-validate:
  request: report-only
paths:
  # Reports invalid requests (top-level fallback).
  # Does not validate responses (default).
  /relaxed:

  # Validates all incoming requests and responses for GET.
  # Reports invalid responses for POST.
  /strict:
    x-flat-validate:
      request: true

    post:
      x-flat-validate:
        response: report-only

    get:
      x-flat-validate:
        response: true

  # No validation whatsoever (overrides top-level)
  /experimental:
    x-flat-validate:
      request: false
      response: false
```

If the validation configuration has to be dynamic (e.g. read an environment variable to active it), you can use [`conf/config.xml`](/reference/configuration.md) instead of `x-flat-validate`.

## System Error Document

FLAT uses an error document for all validation errors. It looks like this:

```json
{
  "error": {
    "message": "Input Validation Failed",
    "status": 400,
    "requestID": "W8chVblhSDR5pk2xwBMUzwAAAA0",
    "info": [
       "Required constraint violated in body for name: The property userName is required."
     ]
  }
}
```
