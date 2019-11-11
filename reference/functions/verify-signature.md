# `verify-signature()`

```
boolean verify-signature(string signature, string data, string public_key [, string algorithm])
```

The `verify-signature` function verifies a given `signature` for the given `data` using the given `public_key` (in PEM format, with or without boundaries).
The optional parameter `algorithm` is the signing algorithm (the default is 'SHA256'; [list of supported algorithms](calc-signature.md#supported-algorithms)).
It returns `true` if the signature could be verified, `false` otherwise.

## Example

```xml
<flow>
  ...
  <eval out="$public_key">$metadata//*[@use = "signing"]//*[local-name() = "X509Certificate"]</eval>
  <template>
  {
    {{$data := 'my data' }}
    {{$signature := '...' }}
    {{$signature_ok := verify-signature($signature, $data, $public_key) }}
    {{$signature_ok := verify-signature($signature, $data, $public_key, 'SHA256') }}
  }
  </template>
<flow>
```
