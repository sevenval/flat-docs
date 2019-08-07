# How can I increase the request timeout to deal with a slow upstream system?

[Various timeout options](https://git.office.sevenval.de/code/pre-couper/extension/blob/develop/docs/actions.md#options) can be set on the `request` action. Here we allow a five second overall timeout for the request:

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

Without that option the request will be aborted after three seconds – FLAT's default timeout setting.
