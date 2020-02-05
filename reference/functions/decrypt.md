# `decrypt()`

```
string decrypt(string ciphertext, string private_key [, string passphrase])
```

The `decrypt` function decrypts the given Base64-encoded `ciphertext` using the given `private_key` (see [below](#private-key-format)).
It uses the [RSAES-OAEP padding scheme](https://en.wikipedia.org/wiki/Optimal_asymmetric_encryption_padding).
The optional parameter `passphrase` is the passphrase for decrypting an encrypted private key (default `""`).
For an unencrypted private key the `passphrase` should be omitted or `""`.

The function returns the decrypted data as a string.

## Example

```xml
<flow>
  <copy out="$encrypted_private_key" in="encrypted_private_key.pem"/>
  <template>
  {
    {{$ciphertext := 'pXdQbl31OMoerOXrEd+nvoaFDv…RQeFScAWCqWCqxTNSKB1lP6C0Hg==' }}
    {{$data := decrypt($ciphertext, $env/PRIVATE_KEY) }}
    {{$data := decrypt($ciphertext, $encrypted_private_key, $env/PASSPHRASE) }}
  }
  </template>
<flow>
```

## Private Key Format

Private keys should be supplied
in [PKCS #8 syntax](https://en.wikipedia.org/wiki/PKCS_8)
using [PEM](https://en.wikipedia.org/wiki/Privacy-Enhanced_Mail) encoding:

```
-----BEGIN PRIVATE KEY-----
…
-----END PRIVATE KEY-----
```

for an unencrypted key – or for an encrypted (i.e. passphrase-secured) key:

```
-----BEGIN ENCRYPTED PRIVATE KEY-----
…
-----END ENCRYPTED PRIVATE KEY-----
```

For such PKCS #8 keys the `-----BEGIN …` and `-----END …` boundaries are optional.
They are required, however, if the private key is stored together with other data,
for example the accompanying public key.

[PKCS #1](https://en.wikipedia.org/wiki/PKCS_1) formatted RSA private keys with boundaries
(`-----BEGIN RSA PRIVATE KEY-----`) are also supported.

## See also

* [`encrypt()`](encrypt.md)
