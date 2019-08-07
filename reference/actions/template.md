# `template` Action

The `<template>` action is the primary tool for generating JSON data
in FLAT's [_Template Language_](../templating/README.md).


## Syntax

* `in`: Location of input data

   Either a file (e.g. `in="conf.json"`) or a variable (`in="$body"`). The input sets the template's initial
   context (`{{.}}`). It may be a JSON or [XML document](../templating/oxn.md).

   `in=""` signals that no input data is used.

   The context can be changed within the template with [`{{with …}}`](../templating/with.md).

   Default: `fit://request/content`

* `out`: Location of output data

   Where the generated JSON should be stored.

   This can be a variable. Variables will hold the _typed result_, such as a JSON object or e.g. a boolean value.
   ```
   <template out="$postBody">
   ```

   Default: `fit://request/content`

* `src`: Location of the template file

   The template definition can be stored in a separate file. The file path is resolved relatively to the flow file.

   ```
   <template src="big.tmpl"/>
   ```

   There is no obligatory file extension. However, we recommend `tmpl` or `tpl`.

* `check`: Check output

   The generated JSON will be parsed and checked. If the template has produced
   an invalid JSON string, an error will be logged.

   The generated text will still be written to the configured `out` location.
   In that case, if `out` is a variable, the output is _not typed_.

   Checking can be disabled with `check="false"`.

   Default: `true`

## Examples


### Accessing JSON data received from client's

The [pre-defined variable `$body`](../variables.md#predefined-variables) holds the HTTP body of incoming `POST` requests.

```xml
<flow>
  <template in="$body">
  {
    "inputData": {{.}}
  }
  </template>
</flow>
```

Request with `curl`:

```sh
$ curl -s -H 'content-type: application/json' --data '{"cool":true}' localhost:8080
 ```

Output:

```json
{
  "inputData": {
    "cool": true
  }
}
```

### Accessing JSON data loaded from upstream servers

In this example, we pass the client's `POST` body to
[httpbin.org](https://httpbin.org/) to have it reflected back.

```xml
<flow>
  <request>
  {
    "url": "https://httpbin.org/anything",
    "method": "POST",
    "body": {
      "src": "$body"
    }
  }
  </request>

  <template>
  {
    "upstreamResponse": {{.}}
  }
  </template>
</flow>
```

Request with `curl`:

```sh
$ curl -s -H 'content-type: application/json' --data '{"cool":true}' localhost:8080
```

Output:
```json
{
  "upstreamResponse": {
    "args": {},
    "data": "{\"cool\":true}",
    "files": {},
    "form": {},
    "headers": {
      "Accept": "*/*",
      "Accept-Encoding": "deflate, gzip",
      "Connection": "close",
      "Content-Length": "13",
      "Content-Type": "application/json",
      "Host": "httpbin.org",
      "User-Agent": "Sevenval FIT"
    },
    "json": {
      "cool": true
    },
    "method": "POST",
    "origin": "78.35.14.35",
    "url": "https://httpbin.org/anything"
  }
}
```

### Using a template file

```xml
<flow>
  <request>{ "url": "https://httpbin.org/anything" }</request>
  <template src="modifyResponse.tmpl"/>
</flow>
```

The file path `modifyResponse.tmpl` is resolved relatively to the flow file.

`modifyResponse.tmpl`:

```json
{
  "ip": {{ origin }},
  "word": {{ method }}
}
```

