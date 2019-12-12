# How can I forward a request to an upstream API?

To forward a request to an upstream API ("proxy" a request), just set the appropriate `url`, copy the request `method` using a [pair producer](/reference/templating/pair-producer.md) and set the request `body` referencing the [client request body `$body`](/reference/variables.md#predefined-variables).

Since we are using the default request ID `main` in the example below, the body together with
its `Content-Type` is automatically set as the response body to be sent
to the client. To correctly forward the upstream response to the client, the HTTP
status code and possibly additional header fields must be relayed explicitly.

```xml
<flow>
  <request>
  {
    {{// the URL for the upstream API}}
    "url": "https://httpbin.org/anything",

    {{// copy the request method and query }}
    {{: $request/method | $request/query }},

    {{// set the request body}}
    "body": {
      "src": "$body"
    },
    {{// if set, copy specific headers, here foo and bar }}
    "headers": {
      {{: $request/headers/foo | $request/headers/bar }}
    },
    {{// if set, copy specific cookies, here SessionID and quux }}
    "cookies": {
      {{: $request/cookies/SessionID | $request/cookies/quux }}
    }
  }
  </request>


  <set-response-headers>
  {
    {{// pass through the upstream response status (defaults to 200) }}
    "status": {{ $upstream/main/status }}{{,}}
    {{// forward certain upstream response header fields }}
    {{: $upstream/main/headers/cache-control | $upstream/main/headers/set-cookie }}
  }
  </set-response-headers>
</flow>
```

The `Content-Type` request and response headers are passed automatically with the body, therefore you don't have to copy them explicitly.

The forwarded request will not have a body if the incoming request does not have one.

## See also

* [`request` action](/reference/actions/request.md) (reference)
