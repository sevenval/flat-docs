# `with`

The template command `{{with _expression_}}` sets the context to the result of `expression` and executes the enclosed expressions or executes the corresponding `{{else}}` block if the expression evaluates to `null`.

Input:
```json
{
  "user": {
    "name": "alice",
  }
}
```

Template:
```xml
<template>
{
  {{with ./user}}
    "Name": {{ name }}
  {{else}}
    "Name": "unknown"
  {{end}}
}
</template>
```

Output:
```json
{
  "Name": "alice"
}
```

## Example: `with` and `json-doc()`

`{{with}}` is especially useful in conjunction with [`json-doc()`](/reference/functions/json-doc.md) to
consolidate data from multiple sources using more compact expressions.

Input:
```json
{
  "location": "home"
}
```

Input `fit://request/request/body`:
```json
{
  "user": "fred",
  "pass": "wilma"
}
```

Template:
```xml
<template>
{
  "realm": {{ location }},
  {{with json-doc("fit://request/request/body") }}
    "user": {{ user }},
    "password": {{ pass }}
  {{end}}
}
</template>
```

Output:
```json
{
  "realm": "home",
  "user": "wilma",
  "password": "fred"
}
```
