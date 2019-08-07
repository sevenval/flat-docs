# `jwt-decode()`

```
OXN-node-set jwt-decode(string jws, string key)
```

validates and unpacks the content of a _JSON web signature_ (JWS) as [OXN](Templating#object-xml-notation). [[RFC 7515](https://tools.ietf.org/html/rfc7515)]
`jws` is expected to be a _compact serialization_. `key` is the matching Base64URL encoded
symmetric key for the signature validation. Currently, signatures created via `HS256`, `HS385` and `HS512` can be validated.

If validation and decoding is successful, you get the original web token as OXN encoded node-set.

If the decoding fails, you get an empty node-set.

Reasons for a decoding failure can be:

* a corrupt JWS,
* an expired time-to-live (JWT `exp` claim),
* an unknown signature algorithm,
* a corrupt JWT (no JSON).

## Example

In this example the JWS is read from the `token`-Cookie in the request: `Cookie: token=eyJ….….…`.
The Base64URL-encoded key `$key` is read from the JSON file `jwt-key.json`: `{"k":"c2VjcmV0"}`.
The unpacked web token is stored in `$jwt`, which provides easy access to its content:

```xml
<template out="$key">{{ json-doc('jwt-key.json')/k }}</template>
<template out="$jwt">{{ jwt-decode($request/cookies/token, $key) }}</template>
<template>{
  "user":   {{ $jwt/user }},
  "expiry": {{ $jwt/exp }}
}
</template>
```
