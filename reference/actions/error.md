# `error` Action

Terminates the flow, sets its body template result as [`$error`](/reference/variables.md#usderror) and calls the [error flow](/reference/OpenAPI/routing.md#error-flow), if configured, or sends the [system error document](/reference/OpenAPI/validation.md#system-error-document) with the body template result as the value of the `error` property.

```xml
<error status="555">{}</error>
```

## Syntax

The `status` attribute defines the HTTP response status code. This is a
shortcut for a [`set-status` action](set-status.md). The default is `500`.

The action body contains a JSON template reprensenting a JSON object.


## Example

```xml
<error status="400">
{
  {{: $request/url }}
  "message": "Something went wrong",
  "code": 4711
}
</error>
```
