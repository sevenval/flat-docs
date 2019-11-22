#  How can I forward a request to an upstream API?

To forward a request to an upstream API ("proxy" a request), just set the appropriate `url`, copy the request `method` using a [pair producer](../reference/templating/pair-producer.md) and set the request `body` referencing the [client request body `$body`](/reference/variables.md#predefined-variables):

```xml
<flow>
  <request>
  {
    {{// the URL for the upstream API}}
    "url": "http://httpbin.org/anything",

    {{// copy the request method}}
    {{: $request/method }} {{,}}

    {{// set the request body}}
    "body": {
      "src": "$body" 
    }
  }
  </request>
</flow>
```

**Note: ** The `Content-Type` request header is passed automatically, so you don't have to copy it.
