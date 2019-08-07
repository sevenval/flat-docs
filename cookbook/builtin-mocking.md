# Using the Built-in Mocking

For each API end point FLAT can generate a mock response with the status code 200 from the respective
[OpenAPI `examples` section](https://swagger.io/docs/specification/2-0/adding-examples/#responses):

```yaml
paths:
  /:
    get:
      responses:
        200:
          examples:
            application/json:
              foo: bar
              baz: qux
```

Or, written as JSON object (Mind the indentation of `}`!):

```yaml
…
          examples:
            application/json: {
                "foo": "bar",
                "baz": "qux"
              }
```

To improve readability, larger objects are better put in the OpenAPI `definitions` section or even into external JSON files:

```yaml
…
          examples:
            application/json:
              $ref: "#definitions/Example"
…
definitions:
  Example:
    foo: bar
    baz: qux
```

or

```yaml
…
          examples:
            application/json:
              $ref: example.json

```

Sending the request header `Mock: true` tells FLAT to respond with a mock:

```bash
$ curl -i -H Mock:true localhost:8080
…
Mock: true
Content-Type: application/json

{"foo":"bar","baz":"qux"}
```

Such a mock response always identifies itself with a `Mock: true` response header line.

A fitting mock response body will be picked from the `examples` section according to the submitted `Accept` header – with `application/json` being the default.

Mock responses will be validated, if response validation is enabled in `config.xml`.
