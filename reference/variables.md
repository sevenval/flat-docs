# Variables

Variables can be used to store intermediate results during [flow](flow.md) execution.
A valid variable name starts with `$` followed by a letter `a`…`z` or `A`…`Z` or an underscore `_`. More letters, underscores, the numbers `0`…`9` or hyphens `-` may follow.


## Predefined Variables

The following predefined variables exist:

* `$body`: client request body
* `$env`: [environment variables](/cookbook/envvars.md)
* `$request`: client request information
* `$server`: server information
* `$upstream`: upstream response information
* `$error`: Contains information regarding the most recent error, but is initially empty.

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

## $error

Both client request and response, as well as upstream request and response validation errors will store information about the error in `$error`. While initially empty, `$error` will have the following properties containing information about the most recent error:

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

## See also

* [Using Env Vars](/cookbook/envvars.md) (cookbook)
