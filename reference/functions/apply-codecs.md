# `apply-codecs()`

```
string apply-codecs(string input, string ...codecs)
```

`apply-codecs` expects at least two `string`s as parameters. The first parameter is the input. The next parameters are the codecs to be applied in sequence, so that the first codec is applied to the given input and the next codecs are applied to the result of the previous encoding process. Returns a string.

The following codecs are supported:

* [base64-encode](base64-encode.md)
* [base64-decode](base64-decode.md)
* [base64url-encode ↗](https://tools.ietf.org/html/rfc7515#section-2)
* [base64url-decode ↗](https://tools.ietf.org/html/rfc7515#section-2)
* [url-encode](urlencode.md)
* [url-decode](urldecode.ms)
* [inflate ↗](https://tools.ietf.org/html/rfc1951)
* [deflate ↗](https://tools.ietf.org/html/rfc1951)


## Example

```xml
<flow>
  <template>
  {
    {{$input := 'my input' }}
    {{$encoded := apply-codecs($input, 'deflate', 'base64-encode', 'url-encode') }}
    <!-- is the same as -->
    {{$deflated := apply-codecs($input, 'deflate') }}
    {{$base64encoded := apply-codecs($deflated, 'base64-encode') }}
    {{$encoded := apply-codecs($base64encoded, 'url-encode') }}
  }
  </template>
<flow>
```
