# `set-status` Action

Set the response's HTTP status code.

## Syntax

```xml
<set-status code="201"/>
```

## Usage

The `code` attribute is mandatory.

If the code is empty or not a number, status `500` will be used.

HTTP status codes defined in
[RFC 7231](https://tools.ietf.org/html/rfc7231#section-6.1) are sent with their
proposed "reason phrase" (e.g. `OK` or `Not Found`).

The flow is not terminated.
