# `error` Action

The `error` action provides a simple and consistent way to handle errors throughout the configuration.

It terminates the flow, sets its body template result as [`$error`](/reference/variables.md#usderror) and calls the [error flow](/reference/OpenAPI/routing.md#error-flow), if configured, or sends the [system error document](/reference/OpenAPI/validation.md#system-error-document) with the body template result as the value of the `error` property.

In its simplest form, an error is triggered like this:

```xml
<error/>
```

To make errors more useful, they should contain a message:

```xml
<error>
{
  "message": "Upstream system sent unusable data"
}
</error>
```

## Error Properties

The action body contains a constant JSON string or a [JSON template](/reference/templating/README.md) representing a JSON object.
The object will be assigned to the `$error` variable.

The following properties of the `$error` object are optional, but must have the specified 
type if set. If not set they will receive the following default values:

* `message`: `string`, default: `'FlowError'`
* `status`: `integer` between 100 and 599, default: `500`
* `code`: `integer` between 0 and 9999, default: `5000`
* `info`: `array` of `string`, default: `["Flow Error triggered"]`

Additional properties are allowed. They will be accessible in the Error Flow. The following example includes the URL in `$error/url`:

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

## Status Code

The `status` property will be used as the HTTP response status code. (The status code may alternatively be defined with the optional `status` attribute).

```xml
<error status="403"/>
```

```xml
<error status="502">
{
  "message": "Upstream system sent unusable data"
}
</error>
```

## Fixed message shortcut

If the action body is only a JSON string (it must be enclosed in double quotes) its value will be
assigned to the `$error/message` property. This is a handy shortcut to trigger simple errors with fixed messages:

```xml
<error status="502"> "Upstream system sent unusable data" </error>
```

## See also

* [Error Flow](/reference/OpenAPI/routing.md#error-flow)
* [`$error`](/reference/variables.md#usderror)
* [Handling Errors](/cookbook/error-flow.md) (Cookbook)
