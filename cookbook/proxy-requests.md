# Proxying requests to Upstream APIs

To proxy requests to an upstream API, you can simply use a combination of two FLAT Swagger enhancements:

* [wildcard paths](/reference/OpenAPI/differences.md#wildcard-paths), and
* [`x-flat-proxy`](/reference/OpenAPI/routing.md#assigning-flat-proxies).

Imagine, the upstream API, you want to use, is located at `https://upstream.api/api/docs` and provides a route to get a document with a certain ID (`/get-doc/{docid}`). To set FLAT as a proxy to this API, the following `swagger.yaml` snippet will do the job:

```yaml
…
basePath: /api
…
paths:
  /docs/**:
    x-flat-proxy:
      origin: https://upstream.api    # replaces the origin
…
```


Assuming a client origin of `https://client.example.com`, a client request to e.g. `https://client.example.com/api/docs/get-doc/42` will be proxied to `https://upstream.api/api/docs/get-doc/42`.

Note, that only the origin is replaced, the path is not modified.

If the upstream API for docs is located at `https://docs.upstream.api/v4` instead of `https://upstream.api/api/docs`, the path has to be adjusted, too:

```yaml
…
basePath: /flat
…
paths:
  /docs/**:
    x-flat-proxy:
      origin: https://docs.upstream.api
      stripEndpoint: true             # strips /flat/docs from the path
      addPrefix: /v4                  # inserts /v4 before the stripped path
…
```

The client request will be proxied to `https://docs.upstream.api/v4/get-doc/42`.

Assuming you want to plug-in a different API (`https://users.upstream/v3`), just add the following:

```yaml
…
  /users/**:
    x-flat-proxy:
      origin: https://users.upstream.api
      stripEndpoint: true             # strips /flat/users from the path
      addPrefix: /v3                  # inserts /v3 before the stripped path
```

A client request to `https://client.example.com/flat/users/profile/4711` will be proxied to `https://users.upstream.api/v3/profile/4711`.
