# Testing API Requests

You have defined your API in `swagger.yaml`, provided a detailed [schema](/reference/OpenAPI/validation.md) and wrote [flows](/reference/OpenAPI/routing.md) for API paths. How can we test the flows?

Let's say you have invented an incredibly useful API route that can tell whether a given number is odd.

## Example

`swagger.yaml`:

```yaml
swagger: '2.0'
basePath: /api
info:
  title: Useful Tools
  version: '1.0'
x-flat-validate:
  request: true
paths:
  /is-odd:
    get:
      x-flat-flow: odd.xml
      description: tells whether {number} is odd
      parameters:
      - name: number
        in: query
        required: true
        description: the number
        type: number
```

Flow in `odd.xml`:
```xml
<flow>
  <if test="$request/get/number = 0">
    <set-response-headers>
    {
      "see-also": "https://en.wikipedia.org/wiki/Parity_of_zero"
    }
    </set-response-headers>
  </if>

  <template>
  {
    "odd":  {{ $request/get/number mod 2 != 0 }}
  }
  </template>
</flow>
```

## Tests

Now let's test that! We could write a [sub-flow](/reference/actions/sub-flow.md) based test like in [Testing Templates](test-templates.md). But this time, we want to actually _call_ our API to make sure that validation works, status codes are correct and so on.

The [`test-request` action](/reference/actions/test-request.md) does just that: You define the API `path` that should be called and all necessary [`request`](/reference/actions/request.md) parameters.

The `test-request` sets these variables so that you can easily inspect the result:

* `$status` the HTTP status code (number)
* `$response` the response body (string)
* `$headers` the response headers (array)

This is what we will use to assert that everything works as expected:

`tests/test-even.xml`:
```xml
<flat-test>
  <test-request>
  {
    "path": "/api/is-odd?number=4"
  }
  </test-request>

  <assert>
  [
    [ "$status", 200, "even is 200, too" ],
    [ "json-parse($response)/odd", false, "4 is not odd" ]
  ]
  </assert>
</flat-test>
```

Note that we have used `json-parse()` to check the `odd` property.

`tests/test-odd.xml`:
```xml
<flat-test>
  <test-request>
  {
    "path": "/api/is-odd?number=4711"
  }
  </test-request>

  <assert>
  [
    [ "$status", 200, "odd is 200" ],
    [ "json-parse($response)/odd", true, "4711 is odd" ]
  ]
  </assert>
</flat-test>
```

`tests/test-zero.xml`:
```xml
<flat-test>
  <test-request>
  {
    "path": "/api/is-odd?number=0"
  }
  </test-request>

  <assert>
  [
    [ "$status", 200 ],
    [ "json-parse($response)/odd", false, "zero is even" ],
    [ "$headers/see-also", "https://en.wikipedia.org/wiki/Parity_of_zero", "aha!" ]
  ]
  </assert>
</flat-test>
```

Run the tests with `flat test`:

```bash
$ flat test tests/*.xml
```
```tap
1..3
ok 1 GET /api/is-odd?number=4 (tests/test-even.xml): 2 assertions
ok 2 GET /api/is-odd?number=4711 (tests/test-odd.xml): 2 assertions
ok 3 GET /api/is-odd?number=0 (tests/test-zero.xml): 3 assertions
passed: 3, failed: 0
```

> 📝
> **Exercise:** Add a test for negative numbers, for numbers with fractional parts (and correct the schema accordingly) and that also verifies that the `see-also` header is not set.


## See also

* [Testing Templates](test-templates.md) (cookbook)
* [Testing Upstream Requests](test-backend.md) (cookbook)
* [Test Framework](/reference/testing/README.md) (reference)
* [`test-request`](/reference/actions/test-request.md) (reference)
* [`assert`](/reference/actions/assert.md) (reference)
