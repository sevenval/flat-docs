# `request` Action

The `request` action carries out a single HTTP or HTTPS request to load content from
remote upstream servers. If you want to do multiple requests in parallel, use the
[`requests` action](requests.md) instead.

## Syntax

* `content="..."` to set the ID for a request (optional, default is `main`)


## Usage

You can use a [JSON template](/reference/templating/README.md) with the features defined below.

Example: GET request

```xml
<request content="my_request">
{
  "url": "http://www.example.com/foo",
  "method": "GET",
  "headers": {
    "User-Agent": "foo",
    "X-Foo": "value 1, value 2"
  },
  "query": {
    "a": 37,
    "b": "c"
  }
}
</request>
```

Example: POST request

```xml
<request content="my_request">
{
  "url": "http://www.example.com/foo",
  "method": "POST",
  "headers": {
    "User-Agent": "foo",
    "X-Foo": "value 1, value 2"
  },
  "post": [
    { "name": "a", "value": "b" },
    { "name": "a", "value": "foo" }
  ]
}
</request>
```

The response body will be written into `fit://request/content/<ID>`.
It can be accessed with the [`content` function](/reference/functions/content.md): `content(<ID>)`.
Additional information about the response, such as headers and status code can
be found in the [`$upstream` variable](/reference/variables.md#predefined-variables).

### `id`

The `id` property sets the request ID.
If `id` is set in the JSON object, is not `null` and not `""`, it will be used and a `content` attribute on the `request` element will be ignored.

### `encoding`

The `encoding` property sets the character encoding used for the URI including the query string and `POST` form data.
Its value must be a string. The default value is `UTF-8`.

### `url`

The `url` property sets the request URL.
If `url` is set in the JSON object, is not `null` and not `""`, it will be used and a `url` attribute on the `request` element will be ignored.

Example:
```json
"url": "http://www.example.com/api/v1/endpoint"
```

### `method`

The `method` property sets the request method. Its value must be a string.
If `method` is set in the JSON object, is not `null` and not `""`, it will be used and a `method` attribute on the `request` element will be ignored.

The default value is `GET`. However, if `post`, `upload` or `body` are set in the JSON object, the default value will be `POST`.

Example:
```json
"method": "POST"
```

### `headers`

The `headers` property sets the request headers. Its value must be a JSON object, with each header field name and value set as properties with values of the JSON objects.
If multiple header fields with the same name should be set, the values must be concatenated in advance.

Example:

```json
"headers": {
  "User-Agent": "foo",
  "content-type": "application/octet-stream",
  "X-Foo": {{ concat("value1", ",", "value2") }}
}
```

### `cookies`

The `cookies` property sets the cookies, i.e. it sets a `Cookie` request header. Its value must be one of the following:

* a JSON object with the cookie key value pairs as properties; or
* an array of JSON objects with `name` and `value` properties; the objects must have both `name` and `value` properties `name` must not be empty; other properties are ignored.

If the value is either a non-empty JSON object or a non-empty array, any `Cookie` header set via `headers` will be ignored.

Example: `cookies` as a JSON object

```json
"cookies": {
  "a": "b",
  "c": "foo"
}
```

Example: `cookies` as an array

```json
"cookies": [
  { "name": "a", "value": "b" },
  { "name": "c", "value": "foo" }
]
```

### `query`

The query string of the request URL can be set as part of `url`.
The `query` property sets the query string explicitly. Its value must be one of the following:

* a string (without the `"?"`);
* a JSON object with the query parameters as properties; or
* an array of JSON objects with `name` and `value` properties; the objects must have both `name` and `value` properties `name` must not be empty; other properties are ignored.

Example: `query` as a string

```json
"query": "a=12&b=foo&a=13"
```

Example: `query` as a JSON object

```json
"query": {
  "a": 37,
  "b": "c"
}
```

Example: `query` as an array
```json
"query": [
  { "name": "a", "value": 12 },
  { "name": "a", "value": "foo" }
]
```

### `post`

The `post` property sets the post parameters (an `x-www-form-urlencoded` request body). Its value must be one of the following:

* a JSON object with the post parameters as properties; or
* an array of JSON objects with `name` and `value` properties; the objects must have both `name` and `value` properties `name` must not be empty; other properties are ignored.

Example: `post` as a JSON object
```json
"post": {
  "a": "b",
  "c": "foo"
}
```

Example: `post` as an array

```json
"post": [
  { "name": "a", "value": "b" },
  { "name": "a", "value": "foo" }
]
```
> 📎
> If both `post` and `body` are set, `post` wins.

### `body`

The `body` property sets the request body, in case the body is not `x-www-form-urlencoded`. Its value must be a JSON object with either

* a `src` property setting the body location (a reference to the actual body content), or
* a `value` property setting the body content.

The optional `mime` property sets the `Content-Type` request header. If no `mime` property is set, the content type is determined automatically.

Example: `body` with `src` using the [predefined `$body` variable](../variables.md#predefined-variables) to pass the incoming request body

```json
"body": {
  "src": "$body"
}
```

Example: body with src using a URL

```json
"body": {
  "src": "fit://site/conf/body.xml",
  "mime": "application/xml+foo"
}
```

Example: `body` with string `value`

```json
"body": {
  "value": "boohooo",
  "mime": "text/plain"
}
```

Example: `body` with JSON `value`

```json
"body": {
  "value": {
    "user": "fred",
    "pass": "wilma"
  },
  "mime": "application/json"
}
```

Example: `body` with template placeholder

```json
"body": {
  "value": {{ $body }},
  "mime": "text/plain"
}
```

> 📎
> If both `body` and `post` are set, `post` wins.

### `uploads`

The `uploads` property sets the content to be uploaded. Its value must be an array of JSON objects with at least

* a `name` property, indicating the field name

and

* either a `src` property, indicating the location of the file
* or a `value` property, containing the content to be uploaded.

The optional `filename` property sets the filename. The optional `mime` property set the MIME type of the uploaded content.

Example: `uploads` with `src` or `value`

```json
"uploads": [
  {
    "name": "foo_html",
    "src": "fit://site/conf/foo.html",
    "filename": "foo.html",
    "mime": "text/html"
  },
  {
    "name": "foo_json",
    "src": "$json",
    "filename": "foo.json"
  },
  {
    "name": "foo_text",
    "value": "some random text",
    "filename": "foo.txt"
  }
]
```

### `options`

The `options` property sets the request options. Its value must be a JSON object. The following options are valid:

* `timeout` - Maximum time in seconds for processing a request (type: `number`, default: `$FLAT_FETCH_DEFAULT_TIMEOUT`)
* `connect-timeout` - Maximum time in seconds for establishing a connection to a server (type: `number`, default: `$FLAT_FETCH_DEFAULT_CONNECT_TIMEOUT)`)
* `time-to-first-byte-timeout` - Maximum time in seconds for receiving the first byte (type: `integer`, default: `$FLAT_DEFAULT_TTFB_TIMEOUT`)
* `send-x-forwarded-for` - Whether to include the client's IP address in an `X-Forwarded-For` header (type: `boolean`, default `false`)
* `set-response-headers` - The HTTP response header fields to set in the response (type: `object` with field name as key and `string` or array of `string` as value)
* `tls-version` - The TLS version (valid values: `"auto"`, `"TLSv1"`, `"TLSv1.0"`, `"TLSv1.1"`, `"TLSv1.2"`)
* `tls-ciphers` - Ciphers to use for TLS connections (type: `string`)
* `ignore-certificate-errors` - Do not check server certificate in order to use upstream services with self-signed or expired TLS certificates (not recommended, use with caution!) (type: `boolean`, default `false`)
* `client-certificate-file` - Path to the client certificate (`.pem`) (type: `string`)
* `client-certificate-password` - The certificate password (type: `string`)
* `transport-protocol` - Protocol for communication with the upstream system (valid values: `http`, `https`)
* `transport-server` - Backend server to communicate with (type: `string`)
* `transport-port` - Port for communication with the upstream system (type: `integer`)
* `proxy-server` - Proxy server hostname with port (type: `string`)
* `proxy-credentials` - Authentication data for the proxy server (type: `string`)
* `use-http-cache` - Whether to enable the HTTP cache (type: `boolean`, default: `false`)
* `respect-client-cache-headers` - Whether to respect incoming cache headers (type: `boolean`, default: `false`)
* `follow-redirects` - Whether to automatically follow redirects (type: `boolean`, default: `false`)
* `max-redirects` - Maximum number of consecutive redirects to follow (type: `integer`)
* `basic-auth-credentials` - Basic Authentication credentials for the upstream system (type: `string`)
* `query-encoding-raw-chars` - Characters in the query string to exclude from the URL encoding (type: `string`)
* `disable-connection-reuse` - Whether to disable the reuse of HTTP connections (type: `boolean`, default: `false`)
* `mock-response` - Whether to mock the response (type: `boolean`, default: `false`)
* `validate-request` - Whether to validate the request (valid values: `true`, `false`, "report-only", default: `false`)
* `validate-response` - Whether to validate the response (valid values: `true`, `false`, "report-only", default: `false`)
* `definition` - The path to the swagger definition file (type: `string`)
* `exit-on-error` - if `true`, abort normal processing of the flow in case of validation errors. If configured, the [error flow](OpenAPI/routing.md#error-flow) is run. Otherwise a standard error message is substituted as a response to the request (type: `boolean`, default `false`)

Example:

```json
"options": {
  "timeout": 5,
  "follow-redirects": true,
  "max-redirects": 10,
  "basic-auth-credentials": "user:s3CreT",
  "validate-response": true,
  "set-response-headers": {
    "Cache-Control": "s-maxage=3600",
    "Set-Cookie": [ "a=1", "b=2" ]
  }
}
```

> 📎
> Any request options set in a `conf/sources.xml` file that match the requested domain and path
> will also be applied to your request. Options directly set in the action have precedence, though.
> We do **not** recommend using `conf/sources.xml`.


## See also

* [Tutorial](/tutorial/README.md#upstream-requests)
* [Sending POST Requests](/cookbook/upstream-post-requests.md)
* [Passing Header Fields](/cookbook/pass-header-field-upstream.md)
* [Increasing the Request Timeout](/cookbook/request-timeout.md)
