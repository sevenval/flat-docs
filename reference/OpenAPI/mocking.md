# Mocking

OpenAPI allows to include _examples_ in the API definition. FLAT uses [response examples](https://swagger.io/docs/specification/2-0/adding-examples/#responses) to implement an easy to use mock server.

Mocking can be activated by the client with the HTTP header

```
Mock: true
```

If given, FLAT will look for the `examples` of the API path's `200` `response` section for an example with a content-type that matches the inbound `Accept` header.

## Example

The [cookbook](/cookbook/README.md) has a recipe for the [built-in mocking](/cookbook/builtin-mocking.md).

## Configuration

To avoid accidently running on mocks in production, mocking can be activated/disabled with the environment variable:

```
FLAT_ALLOW_MOCKING=true
FLAT_ALLOW_MOCKING=false
```
