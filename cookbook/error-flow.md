
# Handling Errors with an Error Flow

The [_error flow_](/reference/OpenAPI/routing.md#error-flow) is triggered when certain
severe errors occur while processing the flow, or when the
[`error` action](/reference/actions/error.md) is invoked. It is typically used to
produce custom error messages or headers, or set the HTTP status. Another example
would be to use it to log additional information using [`fit-log()`](/reference/functions/fit-log.md)

In your `swagger.yaml`, point the top-level property `x-flat-error` to a flow
file:

```yaml
…
x-flat-error:
  flow: error.xml       # ⬅ error flow
…
```

In this example, we use `error.xml` to format a custom error message:

```xml
<flow>
  <template>
    {
      "CustomError":  {
        "Message": {{ $error/message }},
        "Info": {{ $error/info }}
      }
    }
  </template>
  <set-response-headers>
    {
      "Status": {{ $error/status }},
      "Error-Code": {{ $error/code }}
    }
  </set-response-headers>
</flow>
```

This will ensure that all errors that trigger the error flow will produce a consistent status, error message and headers.

## See also

* [Error Flow](/reference/OpenAPI/routing.md#error-flow)
* [`$error`](/reference/variables.md#usderror)
* [`error` action](/reference/actions/error.md)
