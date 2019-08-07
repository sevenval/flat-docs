# How can I pass an arbitrary header field to an upstream system?

Use
[pair producers](../reference/templating/pair-producer.md) to copy the wanted  header fields of the incoming request (`$request/headers`)
to the [`request` action configuration](../reference/actions/request.md).
Here we pass `User-Agent` and `Accept`:

```xml
<flow>
  <request>
  {
    "url": "http://httpbin.org/get",
    "headers": {
        {{: $request/headers/user-agent }}
        {{: $request/headers/accept }}
    }
  }
  </request>
</flow>
```

We can reduce this further to

```xml
    "headers": {
        {{: $request/headers/user-agent | $request/headers/accept }}
    }
```

or maybe even more readable using [`with`](../reference/templating/with.md):

```xml
    "headers": {
        {{with $request/headers }} {{: user-agent | accept }} {{end}}
    }
```
