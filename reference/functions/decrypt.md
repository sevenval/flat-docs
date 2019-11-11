# `decrypt()`

```
string decrypt(string ciphertext, string private_key [, string passphrase])
```

The `decrypt` function decrypts the given `ciphertext` using the given `private_key` (in PEM format, with or without boundaries).
The optional parameter `passphrase` is the pass phrase for decrypting an encrypted private key (the default is the empty string).
It returns a string with the decrypted data.

## Example

```xml
<flow>
  ...
  <template>
  {
    {{$ciphertext := '...' }}
    {{$data := decrypt($ciphertext, $env/MY_PRIVATE_KEY) }}
    {{$data := decrypt($ciphertext, $env/MY_PRIVATE_KEY, $env/MY_PASSPHRASE) }}
  }
  </template>
<flow>
```

## See also

* [`encrypt()`](encrypt.md)
