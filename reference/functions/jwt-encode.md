# `jwt-encode()`

```
string jwt-encode((string|OXN-node-set) jwt, string key [, number time-to-live[, string algorithm]])
```

packs and signs a _JSON web token_ (JWT) as _JSON web signature_ (JWS) in _compact serialization_.
[[RFC 7519](https://tools.ietf.org/html/rfc7519), [RFC 7515](https://tools.ietf.org/html/rfc7515)]

## Parameters

`data`: The payload can be provided as a JSON string or [OXN](Templating#object-xml-notation).

`key`: For the HMAC algorithms, a Base64URL encoded symmetric key for the digital signature. For the `RSASSA` based algorithms, use the PEM encoded *private* key (without the `BEGIN` and `END` lines and without any line breaks). The key must not be password protected.

`time-to-live`: Sets the time span in seconds until expiration in the `exp` claim of the `jwt`. If the TTL is `0`, no `exp` claim is added to the token – it is valid forever. Default: `0` (no expiration).

`algorithm`: The signature or HMAC algorithm to use.

Supported values:
* `HS256`
* `HS384`
* `HS512`
* `RS256`
* `RS384`
* `RS512`

Defaults to `HS256`. Consult [RFC 7518, section 3.1](https://www.rfc-editor.org/rfc/rfc7518.html#section-3.1) for an explanation of the individual values.

The return value is a _JWS compact serialization_ as a string:
```
eyJ….….…
```
An empty string or false will be returned if any errors occur.

## Example

The variable `$json` contains the JSON web token to be packed, the key is read from the environment variable `$JWT_SECRET`. The encoded token is stored in the variable `$jws`.
After 600 seconds the token becomes invalid:

```xml
<template in="$json" out="$jws">
{{ jwt-encode(., $env/JWT_SECRET, 600) }}
</template>
```

A complete example can be found in [Working with JWT](/cookbook/jwt.md).