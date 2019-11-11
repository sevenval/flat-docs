# `calc-signature()`

```
string calc-signature(string data, string private_key [, string passphrase [, string algorithm]])
```

The `calc-signature` function calculates a signature from the given `input` using the given `private_key` (in PEM format, with or without boundaries).
The optional parameter `passphrase` is the pass phrase for decrypting an encrypted private key (the default is the empty string).
The optional parameter `algorithm` is the signing algorithm (the default is 'SHA256').

## Example

```xml
<flow>
  ...
  <template>
  {
    {{$data := 'my data' }}
    {{$signature := calc-signature($data, $env/MY_PRIVATE_KEY) }}
    {{$signature := calc-signature($data, $env/MY_PRIVATE_KEY, $env/MY_PASSPHRASE) }}
    {{$signature := calc-signature($data, $env/MY_PRIVATE_KEY, $env/MY_PASSPHRASE, 'SHA256') }}
  }
  </template>
<flow>
```
