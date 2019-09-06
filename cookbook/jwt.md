# Encoding and Decoding JWT

This snippet demonstrates how to use the `jwt-encode()` and `jwt-decode()` functions to work with [JSON Web Tokens](https://en.wikipedia.org/wiki/JSON_Web_Token).

----

We want to have the JWT Secret (used for signing and verifying a JWS) configurable via an environment variable.

In a development setup, we can simply define a shell variable that starts with `FLAT_`. `flat` will forward all `FLAT_*` variables to the docker container:

```bash
$ FLAT_JWT_SECRET=YXNkZg== flat start
```

The secret must be base64-url encoded. We use our favorite passphrase `asdf` here.

When FLAT is running, we can obtain a token built with user params provided via query params:

```
$ curl localhost:8080/api/jwt?user=alice\&role=admin\&customer=flintstone
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiYWxpY2UiLCJyb2xlIjoiYWRtaW4iLCJjdXN0b21lciI6ImZsaW50c3RvbmUiLCJleHAiOjE1NjA3OTAxMjR9.6eGqRQXGZ1sU9nQz2sBIAHoXJUDe_Vf3TsPdv9pB_9M
```

Later, we can decode and validate the user params with this call:

```
$ curl localhost:8080/api/jwt?token=eyJ0eXAiOiJKV1QiL…
{
  "user": "alice",
  "role": "admin",
  "customer": "flintstone",
  "exp": 1560789571
}
```

You have to be quick copy-pasting it, because the time-to-live (encoded as `exp` param) is set to 20 seconds :)

```xml
<flow>
  <!-- decode and dump content -->
  <if test="$request/get/token">
    <template>
      {{ jwt-decode($request/get/token, $env/FLAT_JWT_SECRET) }}
    </template>
    <break/>
  </if>

  <!-- read user params from query string ?user=…&customer=…&role=… -->
  <template in="$request" out="$data">
    {
      {{: $request/get/user | $request/get/customer | $request/get/role }}
    }
  </template>

  <!-- generate token -->
  <eval out="$jwt">jwt-encode($data, $env/FLAT_JWT_SECRET, 20)</eval>
  <dump in="$jwt" />
</flow>
```



## Pitfalls

For `HMAC` based algorithms, the JWT functions expect the key to be _base64 url encoded_.

But some "bare keys" may already look base64 encoded. [Auth0](https://auth0.com/) for example, uses base64 strings as keys. They need another `base64-encode()` to be used with `jwt-decode()`.

You can do this on-the-fly:

```xml
<template out="$jwt">
  {{ jwt-encode($data, base64-encode($env/FLAT_JWT_SECRET), 20) }}
</template>
```

Of course, you could also do that once outside of FLAT before setting the env var:

```bash
$ echo -n "YXNkZg==" | base64
WVhOa1pnPT0=
$ FLAT_AUTH0_JWT_SECRET=WVhOa1pnPT0= flat start
```

For `RSASSA` based algorithms, the JWT functions expect the key to be PEM encoded, but without the `BEGIN` and `END` lines, and without any line breaks. To generate the private and public keys in this format:

```bash
$ openssl genrsa > privateAndPublic.key
$ tail -n +2 privateAndPublic.key | head -n -1 | tr -d '\n'
MIIEowIB[...]
```

To extract the public key for signature verification in the required format:
```bash
$ openssl rsa -in privateAndPublic.key -outform PEM -pubout -out public.key
$ tail -n +2 public.key | head -n -1 | tr -d '\n'
MIIBIjANBgkqhki[...]
```

## See also

* [`jwt-encode()`](jwt-encode.md)
* [`jwt-decode()`](jwt-decode.md)
* [Working with JWT](/cookbook/jwt.md)
