# Increasing the Request Timeout

In case we have to deal with a slow upstream system, the [various timeout options](../reference/actions/request.md#options) on the `request` action will help. Here we allow a five second overall timeout for the request:

```xml
<flow>
  <request>
  {
    "url": "https://httpbin.org/delay/4",
    "options": {
      "timeout": 5
    }
  }
  </request>
</flow>
```

Without that `timeout` option the request will be aborted after three seconds – FLAT's default timeout setting.
