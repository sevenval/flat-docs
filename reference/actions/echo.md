# `echo` Action

Sends its text content as response body and terminates the request.

```xml
<echo>test</echo>
```

The intended use is error handling or debugging.

## Syntax

The `mime` attribute defines the response `Content-Type`.
Default: `text/plain`

The `status` attribute defines the HTTP response status code. This is a
shortcut for a [`set-status` action](set-status.md).


## Example

```xml
<echo mime="text/json;charset=utf-8" status="400">
{
  "error": {
    "message": "Something went wrong",
    "code": 4711
  }
}
</echo>
```

The action body contains static text. If you need dynamic processing, use
a [`template` action](template.md) (and [`<break/>`](../flow.md#break)) instead.
