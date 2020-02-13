# `encrypt()`

```
boolean encrypt(string data, string public_key)
```

The `encrypt` function encrypts the given `data` using the given `public_key` (see [below](#public-key-format))
and the [RSAES-OAEP padding scheme](https://en.wikipedia.org/wiki/Optimal_asymmetric_encryption_padding).
It returns a Base64-encoded string with the encrypted data.

## Example

```xml
<flow>
  <template>
  {
    {{$x509certificate := "MIIBIjANBgkqhk…4IAcFS9z4SQIDAQAB" }}
    {{$data := 'my data' }}
    {{$ciphertext := encrypt($data, $x509certificate) }}
  }
  </template>
<flow>
```

## Public Key Format

Public keys are expected to be supplied in [PEM](https://en.wikipedia.org/wiki/Privacy-Enhanced_Mail) format.

They can be provided as part of an [X.509 certificate](https://en.wikipedia.org/wiki/X.509),

```
-----BEGIN CERTIFICATE-----
…
-----END CERTIFICATE-----
```

or in [PKCS #8 syntax](https://en.wikipedia.org/wiki/PKCS_8):

```
-----BEGIN PUBLIC KEY-----
…
-----END PUBLIC KEY-----
```

For X.509 certificates the `-----BEGIN CERTIFICATE-----` and
`-----END CERTIFICATE-----` boundaries may be omitted.
They are required, however, if the certificate is stored together with other data,
such as the accompanying private key.

## See also

* [`decrypt()`](decrypt.md)
