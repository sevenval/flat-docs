# `jwt-encode()`

```
string jwt-encode((string|OXN-node-set) jwt, string key [, number time-to-live])
```

packs and signs a _JSON web token_ (JWT) as _JSON web signature_ (JWS) in _compact serialization_.
[[RFC 7519](https://tools.ietf.org/html/rfc7519), [RFC 7515](https://tools.ietf.org/html/rfc7515)]

`jwt` can be provided as a JSON string or [OXN](Templating#object-xml-notation).
`key` is a Base64URL encoded symmetric key for the digital signature. Currently, the only supported signing algorithm is `HS256`.
`time-to-live` sets the time span in seconds until expiration in the `exp` claim of the `jwt`.

The result is a _JWS compact serialization_ as a string `eyJ….….…`.

## Example

The variable `$json` contains the JSON web token to be packed, the key is read from the environment variable `$JWT_SECRET`. The encoded token is stored in the variable `$jws`.
After 600 seconds the token becomes invalid:

```xml
<template in="$json" out="$jws">{{ jwt-encode(., $env/JWT_SECRET, 600) }}</template>
```
