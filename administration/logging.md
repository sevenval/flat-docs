# Logging

FLAT Server writes most of its logs in JSON format to the standard error output (_stderr_). One line is written for each event. I.e. one line each for a client request, an upstream request or an error.

For comfortable log processing, events are discriminated with the `type` field that is one of

* `flat_access`
* `flat_request`
* `flat_alert`

The access log can be augmented with [custom fields](/cookbook/custom-logging.md).

All JSON log lines have a `requestID` field. Its value can be used to correlate log events.

## Access Log

Client requests received by FLAT are recorded as type `flat_access` and have the following format:

```json
{
  "timestamp": "2019-10-15T13:28:26+00:00",
  "type": "flat_access",
  "requestID": "XaXJeWA-@B@3XFNhk42H@QAAAAQ",
  "method": "GET",
  "path": "/api/path",
  "agent": "curl/7.54.0",
  "referrer": "",
  "status": 200,
  "mime": "application/json"
}
```

### Fields

* `timestamp`: Start time of the request in ISO 8601 format
* `type`: Event type discriminator, fixed string `flat_access`
* `requestID`: Log Correlation ID, accessible in flows as [`$request/id`](/reference/variables.md#predefined-variables) and [`$error/requestID`](/reference/variables.md#usderror)
* `method` HTTP method of the client request (`GET`, `POST`…)
* `path`: path and query string of the client request
* `agent`: `User-Agent` header of the client request
* `referrer`: `Referer` [sic] header of the client request
* `status`: HTTP status code of the client response (number) (`200`, `404`, `500`…)
* `mime`: `Content-Type` header of the client response (`text/html`, `application/json`…)


## Request Log

Outgoing [requests](/reference/actions/request.md) to upstream APIs are recorded as type `flat_request` and have the following format:

```json
{
  "timestamp": "2019-10-15T13:28:26+00:00",
  "type": "flat_request",
  "requestID": "XaXJeWA-@B@3XFNhk42H@QAAAAQ",
  "url": "https://example.com/?foo=bar",
  "method": "get",
  "status": 200,
  "mime": "application/json",
  "bytes": 231,
  "duration": 0.607165,
  "id": "main.0",
  "cacheHit": false,
  "refresh": false,
  "timing": {
    "DNS": "17.391",
    "TCP": "96.475",
    "TTFB": "623.931"
  },
  "curlErrorCode": 28,
  "curlErrorMessage": "Operation timed out after 3000 milliseconds with 0 out of -1 bytes received"
}
```

### Fields

* `timestamp`: Start time of the request in ISO 8601 format
* `type`: Event type discriminator, fixed string `flat_request`
* `requestID`: Log Correlation ID
* `url`: Absolute URL of the upstream request including query-string
* `method` HTTP method of the upstream request (`GET`, `POST`…)
* `status`: HTTP status code of the upstream response (number) (`0` for failed requests)
* `mime`: `Content-Type` header of the upstream response (`text/html`, `application/json`…)
* `bytes`: Size of the (possibly compressed) response body in bytes (number) (`0` if the body is empty; when read from the internal cache, the original response length is used)
* `duration`: Time spent waiting for the response in seconds (number)
* `id`: Request/Content identifier as configured with the request's [`id` field](/reference/actions/request.md#id) or `content` attribute (suffixed with `.N` where `N` is incremented for each followed redirect starting with `0`)
* `cacheHit`: Indicating whether the response was read from FLAT's HTTP Cache (boolean) (activated with request option [`use-http-cache`](/reference/actions/request.md#options))
* `refresh`: Indicating whether the response was a `304` response to a request revalidating the local cache (boolean)
* `timing`: Contains timing information about request phases
  * `DNS`: Time to perform name lookup in `ms` (number)
  * `TCP`: Time to establish a TCP connection in `ms` (number) (may be near zero for keep-alive requests)
  * `TTFB`: Time to first byte in `ms` since request was sent (number)
* `curlErrorCode`: [cURL Error Code](https://curl.haxx.se/libcurl/c/libcurl-errors.html) for failed requests (number, optional) (e.g. `28`)
* `curlErrorMessage`: Text error message of _libcurl_ for failed requests (optional)

## Error Log

Errors that have occurred during processing, such as [Validation Errors](/reference/OpenAPI/validation.md) are recorded as type `flat_alert`.

These log events should be monitored!

```json
{
  "timestamp": "2019-10-15T13:28:26+00:00",
  "type": "flat_alert",
  "requestID": "XaXJeWA-@B@3XFNhk42H@QAAAAQ",
  "topic": "validation",
  "message": "Response validation failed: Type constraint violated in body: String value found, but an object is required. "
}
```

### Fields

* `timestamp`: Start time of the request in ISO 8601 format
* `type`: Event type discriminator, fixed string `flat_alert`
* `requestID`: Log Correlation ID
* `topic`: Log topic of the alert, corresponds to [debugging topics](/reference/debugging.md)
* `message`: An informative error message


## Other Log Events

In rare cases plain text log lines may be written to _stdout_ or _stderr_. For example, this happens during startup.

These lines are usually _error log_ messages of the included [Apache Web server](https://httpd.apache.org) that look like this:

```
[Tue Oct 15 13:11:30.190335 2019] [mpm_worker:notice] [pid 28:tid 139763282708608] - AH00292: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips configured -- resuming normal operations
```

## See also

* [Custom Logging](/cookbook/custom-logging.md) (Cookbook)
* [`log` action](/reference/actions/log.md) (Reference)
* [`get-log()` function](/reference/functions/get-log.md) (Reference)
