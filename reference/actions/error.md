# `error` Action

Terminates the flow, sets its body template result as [`$error`](/reference/variables.md#usderror) and calls the [error flow](/reference/OpenAPI/routing.md#error-flow), if configured, or sends the [system error document](/reference/OpenAPI/validation.md#system-error-document) with the body template result as the value of the `error` property.

```xml
<error status="555">{}</error>
```

## Syntax

The optional `status` attribute defines the HTTP response status code. This is a
shortcut for a [`set-status` action](set-status.md). The default is `500`.

The action body contains a constant JSON string or a JSON template representing a JSON object.
The object will be assigned to the `$error` variable.

If the template is a string, it must be enclosed in double quotes (`"`), and its value will be
assigned to the `$error/message` property.

The following properties of the `$error` object are optional, but must have the specified 
type if set. If not set they will receive the following default values:

* `message`: `string`, default: `'FlowError'`
* `status`: `integer` between 100 and 599, default: `500`
* `code`: `integer` between 0 and 9999, default: `5000`
* `info`: `array` of `string`, default: `["Flow Error triggered"]`


## Example

```xml
<error>
{
  {{: $request/url }}
  "message": "Something went wrong",
  "status": 400,
  "code": 4711
}
</error>
```

## Example
```xml
<error status="503"> "A fatal error occurred." </error>
```
