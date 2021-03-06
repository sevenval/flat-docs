# `requests` Action

Sends concurrent requests. Can be configured either by an inline JSON template or by referencing a JSON template in the `src` attribute.

The result of executing the template, whether given inline or referenced, must be a JSON object with the a property for each request ID with a JSON object like defined for the [`request` action](request.md) as its value.

Example: `requests` with inline JSON
```xml
<requests>
{
  "main":{
     "url":"http://example.com",
     "method":"GET",
     …
   },
  "foo":{
     "url":"http://example.com",
     "method":"POST",
     …
  },
  "bar":{
     …
  }
}
</requests>
```

**Note:** An `id` property in the JSON request configuration will be ignored:

```json
{
  "myID":{
     "id":"ignored",
     "url":"http://example.com",
     "method":"GET",
     …
   },
   …
}
```

Example: `requests` with referenced resource

```xml
<requests src="requests.json"/>
```
with requests.json:

```json
{
  "main":{
     "url":"http://example.com",
     "method":"GET",
     …
   },
  "foo":{
     "url":"http://example.com",
     "method":"POST",
     …
  },
  "bar":{
     …
  }
}
```

Example: `requests` with variable

```xml
<template out="$requests">
{
  "main":{
     "url":"http://example.com",
     "method":"GET",
     …
   },
  "foo":{
     "url":"http://example.com",
     "method":"POST",
     …
  },
  "bar":{
     …
  }
}
</template>
<requests src="$requests"/>
```

## See also

* [`request` action](/reference/actions/request.md) (reference)
