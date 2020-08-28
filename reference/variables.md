# Variables

Variables can be used to store intermediate results during [flow](flow.md) execution.
A valid variable name starts with `$` followed by a letter `a`…`z` or `A`…`Z` or an underscore `_`. More letters, underscores, the numbers `0`…`9` or hyphens `-` may follow.


## Predefined Variables

The following predefined variables exist:

* [`$body`](#usdbody): client request body
* `$env`: [environment variables](/cookbook/envvars.md)
* [`$request`](#usdrequest): client request information
* `$server`: server information
* [`$upstream`](#usdupstream): upstream response information
* [`$error`](#usderror): Contains information regarding the most recent error, but is initially empty.

Try the following flow with

```bash
$ curl --data hello localhost:8080 | jq
```

```xml
<flow>
  <template>
  {
    "$request": {
      {{with $request }}{{: * }}{{end}}
    },
    "$body": {{ $body }},
    "$upstream": {{ $upstream }},
    "$server": {
      {{: $server/* }}
    },
    "$env": {
      {{: $env/* }}
    }
  }
  </template>
</flow>
```

The actions [`request`](actions/request.md) and [`requests`](actions/requests.md) set the `$upstream` variable, too:

```xml
<flow>
  <requests>
  {
    "ok": {
      "url": "https://httpbin.org/status/200"
    },
    "failure": {
      "url": "https://httpbin.org/status/500"
    }
  }
  </requests>

  <template>
  {
    "$upstream": {
      {{with $upstream }}{{: * }}{{end}}
    }
  }
  </template>
</flow>
```


```
{
  "$upstream": {
    "ok": {
      "url": "https://httpbin.org/status/200",
      "status": 200,
      "cacheHit": false,
      "headers": { … }
    },
    "failure": {
      …
    }
  }
}
```

## Defining and Accessing Variables

Global variables are usually defined as the output of [`eval`](actions/eval.md) actions.
The variable name is defined in the `out="…"` attribute. It must
begin with `$` followed by a letter and then more letters or numbers.

```xml
<flow>
  <!-- $x = 1 -->
  <eval out="$x">1</eval>
  <!-- $x = $x + 5 -->
  <eval in="$x" out="$x">{{ . + 5 }}</eval>
  <!-- $answer = $x * 7 -->
  <eval out="$answer">{{ $x * 7 }}</eval>
</flow>
```

For structured JSON variables, you can use the [`template`](actions/template.md) action:

```xml
<flow>
  <template out="$cfg">
  {
    "stage": "prod",
    "mock": {{ boolean($request/get/mock) }},
    "answer": {{ $answer }}
  }
  </template>
</flow>
```

Variables may be copied with [eval](actions/eval.md), too:

```xml
<flow>
  <eval out="$client_request">$request</eval>
  …
</flow>
```

### Local Variables

[Templates](/reference/templating/README.md) may define local variables with [`{{$… := …}}`](templating/variable.md). Those variables are
[undefined](#undefined-variables) outside the template they're defined in:

```xml
<flow>
  <template>{{$answer:= 42 }}</template>
  <!-- null -->
  <template>{{ $answer }}</template>
</flow>
```

> 📎
> If a variable containing **binary** content is processed in a
> [`template`](actions/template.md) or [`xslt` action](actions/xslt.md),
> its content will probably end up being truncated, garbled or both.

## Undefined Variables

Attempting to access a variable that has not been set previously will yield
an empty node-set. The empty node-set will be evaluated to `false` in
conditions and produces the string `null` in placeholders:

```xml
<flow>
  <template>
    {{if $undefined }}
      will never be reached
    {{else}}
      <!-- null -->
      {{ $undefined }}
    {{end}}
  </template>
</flow>
```

## `$body`

The `$body` variable contains the request body.

If the request body is JSON (`Content-Type: application/json`) `$body` contains the parsed JSON. You can access its properties with XPath expressions with a `json` segment before the top-level properties. E.g.

```json
{
  "foo": 1,
  "bar": {
    "baz": true
  }
}
```
The value for `foo` can be accessed by `$body/json/foo`, the value for `baz` by `$body/json/bar/baz`.

In other cases the content is stored in `$body` as a string and cannot be accessed by XPath.


## `$request`

The `$request` variable contains information about the incoming client request, such as the URL, the request header fields and possibly the query component or cookies, if any were sent.

Example:
```xml
<request>
  <method>POST</method>
  <purpose>main</purpose>
  <debug/>
  <host>localhost</host>
  <port>12345</port>
  <id>XeeSVJ5AFt8VyXYagp3lvgAAACc</id>
  <url>http://localhost:12345/api/proxy/foo?a=b&amp;c=d</url>
  <path>/api/proxy/foo</path>
  <query>a=b&amp;c=d</query>
  <headers>
    <host>localhost:12345</host>
    <user-agent>curl/7.64.0</user-agent>
    <accept>*/*</accept>
    <cookie>NAME1=VALUE1; NAME2=VALUE2</cookie>
    <content-type>application/x-www-form-urlencoded</content-type>
    <foo>asdf</foo>
    <bar>qwer</bar>
    <content-length>17</content-length>
  </headers>
  <get>
    <a>b</a>
    <c>d</c>
  </get>
  <cookies>
    <NAME1>VALUE1</NAME1>
    <NAME2>VALUE2</NAME2>
  </cookies>
  <endpoint>/api/proxy</endpoint>
</request>
```

As HTTP request headers are defined to be case-insensitive, their names are lower-cased for convenient access even if the client has sent the field name with upper-case letters, e.g.:

```
$request/headers/user-agent
```

If a client URL path is matched by a [wildcard path](/reference/OpenAPI/differences.md#wildcard-paths), `$request/endpoint` is the path part preceding the part matched by `/**`. Otherwise, `$request/endpoint` is the same as `$request/path`.

```yaml
…
basePath: /api
…
paths:
  /**:
    …
  /foo/**:
    …
  /foo/qux:
    …
  /foo/{p1}:
    …
```

| Client URL | matches | `$request/path` | `$request/endpoint` |
| --- | --- | --- | --- |
| https://example.com/api/foo/qux | /foo/qux | /api/foo/qux | /api/foo/qux |
| https://example.com/api/foo/quuux | /foo/{p1} | /api/foo/quuux | /api/foo/quuux |
| https://example.com/api/foo/bar/qux | /foo/** | /api/foo/bar/qux | /api/foo |
| https://example.com/api/bar | /** | /api/bar | /api |


## `$upstream`

The `$upstream` variable contains information about upstream responses. The properties for each upstream response are stored with the request ID ([`id` property](/reference/actions/request.md#id) or [`content` attribute](/reference/actions/request.md#syntax)).

* `url` - The request URL (string)
* `status` - The response status code (integer)
* `cacheHit` - `true` if the response was served from a cache (see [`use-http-cache` or `force-cache-ttl` request options](/reference/actions/request.md#options)), `false` otherwise
* `headers` - The response headers, each with a lower-cased field name

Example:
```xml
<upstream>
  <request1>
    <url>https://httpbin.org/status/200</url>
    <status number="">200</status>
    <cacheHit>false</cacheHit>
    <headers>
      <date>Thu, 27 Aug 2020 14:12:33 GMT</date>
      <content-type>text/html; charset=utf-8</content-type>
      <connection>keep-alive</connection>
      <server>gunicorn/19.9.0</server>
      <access-control-allow-origin>*</access-control-allow-origin>
      <access-control-allow-credentials>true</access-control-allow-credentials>
    </headers>
  </request1>
  <request2>
    <url>https://httpbin.org/status/500</url>
    <status number="">500</status>
    <cacheHit>false</cacheHit>
    <headers>
      <date>Thu, 27 Aug 2020 14:12:33 GMT</date>
      <content-type>text/html; charset=utf-8</content-type>
      <connection>keep-alive</connection>
      <server>gunicorn/19.9.0</server>
      <access-control-allow-origin>*</access-control-allow-origin>
      <access-control-allow-credentials>true</access-control-allow-credentials>
    </headers>
  </request2>
</upstream>
```

To check, for example, if the status code of the response with the ID `myRequest` is successful you can use the following XPath expression:

```
$upstream/myRequest/status = 200
```


## `$error`

Client request and response validation, upstream connection and request and response validation errors, and those triggered by the [`error` action](/reference/actions/error.md) will store information about the error in `$error`. While initially empty, `$error` will have the following properties containing information about the most recent error, unless it is triggered by the `error` action:

* `status` - the HTTP status that is used by default for responses if the error was passed to the client (type: `number`)
* `code` - an error code (type: `number`)
* `message` - a single line of text describing the error (type: `string`)
* `info` - detailed information about the error (type: `array` of `string`)
* `requestID` - the requestID as it should appear in the logs (type: `string`)

Example:
```json
{
  "status": 400,
  "code": 3204,
  "message": "Input Validation Failed",
  "info": [
    "Path /api/empty-body/ not found."
  ],
  "requestID": "XYOGvOu@c2mhpIlgFB-yPwAAAF8"
}
```

If set by the `error` action, `$error` contains the [JSON template result](/reference/templating/README.md) of the action's element body.

## See also

* [Using Env Vars](/cookbook/envvars.md) (cookbook)
