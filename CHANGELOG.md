# Changelog

## Unreleased

### Fixed

- [`set-env`](/reference/actions/set-env.md) now does not produce unnecessary quotes for numeric values


## [20210107](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Added

- The [`uuid3()` and `uuid4()` functions](/reference/functions/uuid.md)
- The [`ldap-query()` function](/reference/functions/ldap-query.md)
- [LDAP TLS configuration](/reference/configuration.md#ldap-tls-configuration) and [LDAP timeout](/reference/configuration.md#ldap-timeout)
- The [`scope-claim`](/reference/OpenAPI/security.md#the-x-flat-jwt-field) and [`post-check-flow`](/reference/OpenAPI/security.md#the-x-flat-jwt-field) properties
- Specifying the [required token scope](/reference/OpenAPI/security.md#applying-security-schemes)
- [Merging directives into `php.ini`](/administration/configuration.md#php-ini) via environment variables

### Fixed

- [Path parameters](/reference/OpenAPI/routing.md#path-parameters) were not usable in [error flows](/reference/OpenAPI/routing.md#error-flow)


## [20200828](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Added

- [`FLAT_DEBUG_ALLOW_HEADER`](/reference/debugging.md#per-request-debugging) to enable debugging using the `Debug` request header, defaults to `false`
- The [request option](/reference/actions/request.md#options) `force-cache-refresh`
- The [`ldap-lookup()` function](/reference/functions/ldap-lookup.md)
- The `cacheHit` property in the [upstream response information (`$upstream`)](/reference/variables.md#usdupstream)

### Fixed

- Empty objects are no longer [logged](/cookbook/custom-logging.md#adding-a-log-field) as empty arrays.
- The [`json-to-csv()` function](/reference/functions/json-to-csv.md) allows `null` values in array entry objects.

### Changed

The [`log` action](/reference/actions/log.md) can no longer override [system log fields](/administration/logging.md#fields).


## [20200519](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Added

- [Beta image](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags?name=beta) now publicly available. [More about Docker images…](/administration/docker.md)
- Warnings in [debug log](/reference/debugging.md) about invalid [Swagger definitions](/reference/openapi/differences.md)
- Validation for the [`assert`](/reference/actions/assert.md) and [`set-env`](/reference/actions/set-env.md) test action configurations.
- The [`error` action](/reference/actions/error.md)
- additional configuration options for the [PHP-FPM process management](/administration/configuration.md)
- [`out-header`](/reference/OpenAPI/security.md#forwarding-jwt-upstream) property for easy JWT forwarding

### Fixed

-  Calls to the [`content()` function](/reference/functions/content.md) affecting the result of the [`body()` function](/reference/functions/body.md)


## [20200424](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Added

- [Swagger `security` requirements](/reference/OpenAPI/security.md) can now also be specified at the path level.
- [`x-flat-proxy`](/reference/OpenAPI/routing.md#assigning-flat-proxies) to configure proxies without a flow
- Enhanced [`proxy-request` action](/reference/actions/proxy-request.md) with `origin`, `query`, `stripEndpoint` and `addPrefix` properties

### Fixed

- If a client URL path is below the API base path, does not match any defined route, and a path is defined which equals the API base path, so that a matching client URL path is the concatenation of the API base path with itself (e.g. `/api/api` if the `basePath` is `/api`), the [fallback flow](/reference/OpenAPI/routing.md#fallback-flow) is now properly executed.
- Some PEM formatted keys could not be recognized during [JWT processing](/cookbook/jwt.md).
- Multi-line values for [environment variables](/cookbook/envvars.md) are now supported.

### Changed

- If the `definition` [request option](/reference/actions/request.md#options) is given with either a [`proxy-request` action](/reference/actions/proxy-request.md) or [`x-flat-proxy`](/reference/OpenAPI/routing.md#assigning-flat-proxies), the defaults for the `exit-on-error`, `validate-request` and `validate-response` request options are changed to `true`.


## [20200409](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Added

- The [`json-to-csv()` function](/reference/functions/json-to-csv.md)
- The FLAT revision is shown when FLAT is started and is available in [`$env/FLAT_REVISION`](/reference/variables.md)

### Changed

- Enhanced [`flat_access` log](/administration/logging.md#access-log) with new fields

### Fixed

- When [testing](/reference/testing/README.md) multiple test files with `flat test`, each test now tests its own response.
- Fatal errors when using certain combinations of [`jwt-decode()`](/reference/functions/jwt-decode.md) and [`<eval/>`](/reference/actions/eval.md)


## [20200323](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Fixed

- [Environment variables](/cookbook/envvars.md) are shown in the debug log if the [debug topic](/reference/debugging.md) is `env`
- With activated [upstream validation](/tutorial/README.md#upstream-validation), a missing `definition` [option](/reference/actions/request.md#options) or a `definition` value referencing a non-existant resource now results in a 500 response with a proper error message.
- Swagger security scheme objects without `x-flat-jwt` are ignored for [security checks](/reference/OpenAPI/security.md).


## [20200318](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Added

- [`body()` function](/reference/functions/body.md)
- [`pass-body` action](/reference/actions/pass-body.md)
- [Security checks with JWT](/reference/OpenAPI/security.md).

### Changed

- [`set-response-headers` action](/reference/actions/set-response-headers.md) now accepts the empty object `{}`
- Reading [`swagger.yaml`](/reference/OpenAPI/README.md) is faster because of caching


## [20200213](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Added

- Validation for `application/x-www-form-urlencoded` encoded [`formData` parameters](https://swagger.io/docs/specification/2-0/describing-parameters/#form-parameters)
- The [`proxy-request`](/reference/actions/proxy-request.md) action
- The functions [`verify-xmldsig()`](/reference/functions/verify-xmldsig.md) and [`decrypt-xml()`](/reference/functions/decrypt-xml.md).

### Fixed

- Parameter handling of the functions [`decrypt()`](/reference/functions/decrypt.md) and [`calc-signature()`](/reference/functions/calc-signature.md).

### Changed

- Padding scheme for [`encrypt()`](/reference/functions/encrypt.md) and [`decrypt()`](/reference/functions/decrypt.md) to [RSAES-OAEP](https://en.wikipedia.org/wiki/Optimal_asymmetric_encryption_padding).
- Relative paths in the [`json-doc()` function](/reference/functions/json-doc.md) are resolved relative to the flow file's path.

## [20200110](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Added

- The [Swagger extension](reference/OpenAPI/differences.md#x-flat-extensions) `x-flat-validate` is now also recognized below `paths/<path>` and `paths/<path>/<operation>`.
- The `force-cache-ttl` [request option](/reference/actions/request.md#options)

### Changed

- Only allow operations defined in [OpenAPI version 2.0](https://swagger.io/specification/v2/#pathItemObject) to be used in the [`swagger.yaml`](/reference/OpenAPI/README.md)

### Fixed

- The default value for the `use-http-cache` [request option](/reference/actions/request.md#options) is now false, even if no request options are configured.
- Segmentation fault (or double free) when [eval](/reference/actions/eval.md) is used to assign nodes from a node-set variable to another variable


## [20191210](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags)

### Added

- The functions [`apply-codecs()`](/reference/functions/apply-codecs.md), [`encrypt()`](/reference/functions/encrypt.md), [`decrypt()`](/reference/functions/decrypt.md), [`calc-signature()`](/reference/functions/calc-signature.md) and [`verify-signature()`](/reference/functions/verify-signature.md)
- The function [`file-exists()`](/reference/functions/file-exists.md)
- The [`$error`](/reference/variables.md#usderror) variable is set and [`exit-on-error`/`error flow` handling](/cookbook/error-flow.md) is triggered if a request error occurs
- The [`id`](/reference/actions/request.md#id) and [`encoding`](/reference/actions/request.md#encoding) properties in the JSON request configuration
- More environment variables for system [configuration and tuning](/administration/configuration.md)
- If a path in [`swagger.yaml`](/reference/OpenAPI/differences.md#wildcard-paths) ends with `/**`, this entry matches the given path as well as arbitrary paths below it.

### Changed

- [Swagger validation](/reference/OpenAPI/validation.md) now gracefully accepts empty objects in the definition.
- Logging of [template results](/reference/templating/README.md) for more [flow actions](/reference/actions/README.md)

### Fixed

- Some alert messages were [logged](/administration/logging.md) twice
- Evaluating an undefined or `null` [variable](/reference/variables.md), as a string, now returns the empty string instead of the string `null`
- Incorrect default content-type `text/xml` for request bodies
- The [`set-response-headers` action](/reference/actions/set-response-headers.md) now replaces `Cache-Control` headers instead of merging them
- The [`serve` action](/reference/actions/serve.md) now correctly handles whitespace and other URL-Encoded characters in the name of the `fallback-doc`


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
