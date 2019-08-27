# Testing with Backend Requests

Usually, our [flows](/reference/flow.md) contain [_upstream requests_](/reference/actions/request.md) to one or more backends that we use to provide the client response. Sometimes upstream requests use results of other upstream requests. Testing such flows can be a challenge.

In this example flow, we use the helpful tool [httpbin.org](https://httpbin.org/) to reflect a parameter.

The Swagger snippet is

```yaml
basePath: /api
paths:
  /dashboard:
    x-flat-flow: dashboard.xml
    get:
      parameters:
        - name: count
          in: query
          description: number of notifications
          type: number
```

and the flow in `dashboard.xml`:
```xml
<flow>
  <request content="notifications">
   {
    "url": "https://httpbin.org/anything",
    "headers": {
      "accept": "application/json"
    },
    "query": {
      "noti": {{ $request/get/count ?? 1 }}
    }
  }
  </request>
  <break if="$upstream/*/error" status="500" />

  <template>
    {{$rsp := content("notifications")/json }}
    {
      "sites": [],
      "notifications": {{ number($rsp/args/noti) ?? 2 }}
    }
  </template>
</flow>
```

We use the client's `count=` parameter (or `1` if missing) to send it as `noti=` to `httpbin.org`.

A response for that upstream request could look like this:

```json
{
  "args": {
    "noti": "8"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Host": "httpbin.org", 
    "User-Agent": "curl/7.54.0"
  }, 
  "method": "GET", 
  "url": "https://httpbin.org/anything?noti=8"
}
```

The parameter is reflected back in the JSON responses `args` property. That is what we use in the response template, where it is finally called `notifications`.

## Mocking the Backend

A naive approach to writing a [flat test](/reference/testing/README.md) would be a `test-request` with some assertions.

`tests/test-notifications.xml`:

```xml
<flat-test>
  <test-request>
  {
    "path": "/api/dashboard",
    "query": {
      "count": 5
    }
  }
  </test-request>

  <assert>
  [
    [ "json-parse($response)/notifications", 5 ]
  ]
  </assert>
</flat-test>
```

This actually works. However, our flow in fact sends the upstream request to `httpbin.org`. The test will fail, if their server is down. And if we were mocking a single-sign-on service, we would need real credentials. That's not how we like our automated tests to work.

The [`backend-flow` test action](/reference/actions/backend-flow.md) provides a mean mock backend responses.

We extend our `flat-test` with this `backend-flow` call as its first action:

```xml
<flat-test>
  <backend-flow request="state">
    <template>
    {
      "args": {
        "noti": 5
      }
    }
    </template>
  </backend-flow>
  …
  ```

A `backend-flow` allows us to create a fake response for the upstream requests. We can use regular [actions](/reference/actions/README.md) to set headers, status code, response bodies and so on.

Note, that the `backend-flow` action only _registers_ that flow for subsequent `request` actions. That's why we have to put it first.

You can register multiple backend flows for your requests. Upstream requests and flows are matched using their `request` ID. In our case, `notifications`.

## Asserting Upstream Requests

Injecting a mocked response into your flow only allows to test one direction: downstream. How do we test, that our flow has sent the right parameters to the upstream server?

You can use `assert` in `backend-flow`, too:

```xml
  <backend-flow request="notifications">
    <assert>
    [
      [ "$request/get/noti", 5 ]
    ]
    </assert>
    <template>
    {
      "args": {
        "noti": 5
      }
    }
    </template>
  </backend-flow>
```

This test will fail, if we don't pass the `count` parameter as expected.

For this assertion we have made use of the [pre-defined variable `$request`](/reference/variables.md#predefined-variables). You can use `$body` to inspect the upstream request body. These variables are overridden in backend flows and restored afterwards. All other global variables are shared between flow, backend flow and test flow! This allows for dynamic backend mocks and more assertion possibilities.

## See also

* [Testing API Requests](test-api-request.md) (cookbook)
* [Testing Templates](test-templates.md) (cookbook)
* [Test Framework](/reference/testing/README.md) (reference)
* [`assert`](/reference/actions/assert.md) (reference)
* [`backend-flow`](/reference/actions/backend-flow.md) (reference)
* [`test-request`](/reference/actions/test-request.md) (reference)