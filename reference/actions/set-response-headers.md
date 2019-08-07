# `set-response-headers` Action

This action sets header fields in the client response.

## Syntax

The configuration uses
JSON format with the header field names as keys (`string`) and values of type
`string`, `number`, `boolean` or `null`. You can provide an `array` to set multiple values.

The action can have an `in` attribute (input), but no `out` attribute (output).
It may also have a `src` attribute referencing a JSON template and a `status` attribute.

## Usage

A response status can be set via a `Status` header field or with the `status` attribute – the latter overrides the former. This will send a 404 response:

```xml
<set-response-headers status="404">
  { "Status": 200 }
</set-response-headers>
```


## Example

```xml
<set-response-headers in="$upstream">
{
  "content-type": "text/plain" {{,}}
  {{with myRequest/headers }}
    {{: foo }}
    "set-cookie": [
      {{loop array(set-cookie) }}
        {{ . }}
      {{end}}
    ]
  {{end}}
}
</set-response-headers>
```

This configuration sets the `content-type` to `text/plain`, copies the
`foo` header field from the `myRequest` upstream response and also copies all
`set-cookie` header fields from that upstream response. If no `set-cookie`
header fields are in the upstream response, none will be set in the client
response.

```xml
<set-response-headers src="my-response-headers.tmpl"/>
```

This configuration references an external resource with a JSON template for the
response headers.
