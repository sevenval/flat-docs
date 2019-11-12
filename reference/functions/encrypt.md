# `encrypt()`

```
boolean encrypt(string data, string public_key)
```

The `encrypt` function encrypts the given `data` using the given `public_key` (in PEM format, with or without boundaries).
It returns a Base64-encoded string with the encrypted data.

## Example

```xml
<flow>
  <eval out="$public_key">$metadata//*[@use = "encryption"]//*[local-name() = "X509Certificate"]</eval>
  <template>
  {
    {{$data := 'my data' }}
    {{$ciphertext := encrypt($data, $public_key) }}
  }
  </template>
<flow>
```

## See also

* [`decrypt()`](decrypt.md)
