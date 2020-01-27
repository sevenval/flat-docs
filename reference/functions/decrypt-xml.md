# `decrypt-xml()`

```
boolean decrypt-xml(encrypted_data, string private_key [, string passphrase])
```

Decrypts an `EncryptedData` XML element which was encrypted using [XML Encryption](https://www.w3.org/TR/xmlenc-core1/) and
*replaces* it with its unenrypted counterpart.

* `encrypted_data`: a node-set containing the `EncryptedData` element in the `http://www.w3.org/2001/04/xmlenc#` namespace as its first node,
or an encrypted DOM document or an XML string. Only the first node of a provided node-set will be decrypted.
* `private_key`: Private key to decrypt the symmetric encryption key provided in the `EncryptedKey` element.
  See [`decrypt()`](decrypt.md#private-key-format) for the expected format of the private key.
* `passphrase` (optional): passphrase for decrypting an encrypted `private_key` (defaults to `""`).

Returns `true()` if `encrypted_data` was successfully decrypted, `false()` otherwise.

## Example

```xml
<flow>
  …
  <eval out="$document">xml-parse($encrypted-xml-string)</eval>

  <template>
  {{if decrypt-xml($document, $env/PRIVATE_KEY, $env/PASSPHRASE]) }}
    Decrypted ciphertext: {{ $document }}
  {{else}}
    Decryption failed!
  {{end}}
  </template>
<flow>
```
