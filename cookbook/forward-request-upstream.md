# How can I forward a request to an upstream API?

To forward a request to an upstream API ("proxy" a request), just set the appropriate `url`, copy the request `method` using a [pair producer](../reference/templating/pair-producer.md) and set the request `body` referencing the [client request body `$body`](/reference/variables.md#predefined-variables).

In this case, since we are using the request ID `main`, the body together with
its content-type is already automatically set as the response body to be sent
to the client. To correctly forward the upstream response to the client, the HTTP
status code and possible additional header fields must be relayed explicitly.

```xml
<flow>
  <request>
  {
    {{// the URL for the upstream API}}
    "url": "http://httpbin.org/anything",

    {{// copy the request method}}
    {{: $request/method }},

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
    "status": {{ $upstream/main/status }},
    {{: $upstream/main/headers/set-cookie }}
  }
  </set-response-headers>
</flow>
```

**Note: ** The `Content-Type` request header is passed automatically, so you don't have to copy it.
**Note: ** The forwarded request will not have a body if the incoming request does not have one.
