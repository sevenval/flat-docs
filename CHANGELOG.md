# Changelog

## Unreleased

### Added

- The [`id`](/reference/actions/request.md#id) and [`encoding`](/reference/actions/request.md#encoding) properties in the JSON request configuration


## [20191018](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Added

- Swagger definition supports [`discriminator`](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#composition-and-inheritance-polymorphism),  [JSON schema `$id` references](https://json-schema.org/understanding-json-schema/structuring.html#using-id-with-ref) and [JSON schema `propertyNames`](https://json-schema.org/understanding-json-schema/reference/object.html#property-names)
- The [`array-reverse()`](/reference/functions/array-reverse.md) and [`sort()`](/reference/functions/sort.md), [`xml-parse()`](/reference/functions/xml-parse.md) and [`html-parse()`](/reference/functions/html-parse.md) functions
- Validation of the [`request`](/reference/actions/request.md), [`requests`](/reference/actions/requests.md) and [`set-response-headers` action](/reference/actions/set-response-headers.md) JSON bodies
- The expected result in an [`assert` action](/reference/actions/assert.md)'s assertion can now be `null`
- The [`log` action](/reference/actions/log.md), the [`get-log()` function](/reference/functions/get-log.md)

### Changed

- The test search for [flat-test](/reference/testing/) is recursive
- [Logs](/administration/logging.md) are sent to stderr in JSON format

### Fixed

- The `report-only` [validation modes](/reference/OpenAPI/validation.md)
- The `exit-on-error`, `mock` and `validate` [request options](/reference/actions/request.md#options) also for XML-configured requests
- Relative paths for e.g. `in` with `copy` in [`backend-flows`](/reference/actions/backend-flow.md)


## [20190919](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Added

- The [`$error`](/reference/variables.md#usderror) variable containing error information for client request/response validation errors
- The [error flow](/cookbook/error-flow.md), called if an error occurs, and referenced by `flow` in `x-flat-error` in the swagger.yaml
- The `exit-on-error` [request option](/reference/actions/request.md#options) (for JSON-configured requests) to trigger the error flow
- An additional parameter `algorithm` for the [`jwt-decode()`](/reference/functions/jwt-decode.md) function to limit the acceptable signing algorithms. Mandatory for RSASSA based signatures
- The `contains` and `pattern` [compare flags](/reference/actions/assert.md#compare-flags) for the `assert` action
- The `flat test` Framework with [`assert`](/reference/actions/assert.md), [`test-request`](/reference/actions/test-request.md), [`backend-flow`](/reference/actions/backend-flow.md) and [`set-env`](/reference/actions/set-env.md) actions
- The [`json-stringify()`](/reference/functions/json-stringify.md) and [`json-parse()`](/reference/functions/json-parse.md) functions

### Changed

- The default `User-Agent` for [upstream requests](/reference/actions/request.md) is `FLAT`
- Unless `terminate="false"` is set, the [`serve` action](/reference/actions/serve.md) will terminate the flow
- For the [`request` action](/reference/actions/request.md): values in [`headers`](/reference/actions/request.md#headers) may now also be numeric or boolean
- If the signature cannot be created, the [`jwt-encode()`](/reference/functions/jwt-encode.md) function returns an empty string and an error message is logged
- The `key` for the [`jwt-encode()`](/reference/functions/jwt-encode.md) and [`jwt-decode()`](/reference/functions/jwt-decode.md) functions must not be empty
- HTML [error page](/tutorial/README.md#getting-started) only if HTML is accepted; plain text otherwise

### Fixed

- Fatal error when creating requests with `null` [query parameter](/reference/actions/request.md#query)
- Fatal error when creating requests with invalid [body source](/reference/actions/request.md#body)
- Requests are now rejected if upstream validation is enabled, but no [`definition` option](/reference/actions/request.md#options) is configured or the given definition is not found
- The results of the [`split()`](/reference/functions/split.md) function can now be used as input for [`join()`](/reference/functions/join.md) or [`fit-serialize()`](/reference/functions/fit-serialize.md)
