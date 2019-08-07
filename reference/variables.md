# Variables

Variables can be used to store intermediate results during [flow](flow.md) execution.
A valid variable name starts with `$` followed by a letter `a`…`z` or `A`…`Z` or an underscore `_`. More letters, underscores, the numbers `0`…`9` or hyphens `-` may follow.


## Predefined Variables

The following predefined variables exist:

* `$body`: client request body
* `$env`: environment variables
* `$request`: client request information
* `$server`: server information
* `$upstream`: upstream response information

Try the following flow with

```
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

Global variables are defined as the output of [`template` actions](actions/template.md)
with `out="…"`. Their values are evaluated inside [placeholder expression `{{ … }}`](templating/placeholder.md):

```xml
<flow>
  <template out="$x">1</template>                   <!-- $x = 1 -->
  <template in="$x" out="$x">{{ . + 5 }}</template> <!-- $x = $x + 5 -->
  <template out="$answer">{{ $x * 7 }}</template>   <!-- $answer = $x * 7 -->
  <template>{{ $answer }}</template>
</flow>
```

Variables may be [copied](actions/copy.md), too:

```xml
<flow>
  <copy in="$request" out="$client_request"/>
  …
</flow>
```

Local variables created with [`{{$… := …}}`](templating/variable.md) are
[undefined](#undefined-variables) outside the template they're defined in:

```xml
<flow>
  <template>{{$answer:= 42 }}</template>
  <template>{{ $answer }}</template>      <!-- null -->
</flow>
```

> 📎
> If a variable containing **binary** content is processed in a
> [`template`](actions/template.md) or [`xslt` action](actions/xslt.md),
> its content will probably end up being truncated, garbled or both.

## Undefined Variables

Attempting to access a variable that has not been set previously will yield
an empty node-set. The empty node-set will be evaluated to `false` in
conditions and to `null` in placeholders:

```xml
<flow>
  <template>
    {{if $undefined }}
      will never be reached
    {{else}}
      {{ $undefined }}       <!-- null -->
    {{end}}
  </template>
</flow>
```
