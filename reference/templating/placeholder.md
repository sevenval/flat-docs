# Placeholder


Values of JSON-Objects are addressed using property names separated by a slash (`/`).


> 📎
> Note that the `placeholder` requires whitespace between the opening `{{` and the expression. All other template commands must immediately follow the `{{`.

### Example: Placeholder

Input:
```json
{
  "account": {
    "name": "alice",
    "mail": "alice@example.com",
    "id": 4711
  }
}
```

Template:
```xml
<template>
{
  "user": {{ account/name}},
  "id": {{ account/id }},
  "admin": {{ account/id = 0 }}
}
</template>
```

Result:
```json
{
  "user": "alice",
  "id": 4711,
  "admin": false
}
```

The placeholder generates typed output. For example, no quotes (`""`) should be used to enclose the `{{ … }}` expression, since a string result will automatically be emitted with quotes during execution of the template. Without explicit quotes, all JSON types, including numbers, boolean values or arrays and objects can be produced correctly.
