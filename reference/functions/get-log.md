# `get-log()`

```
OXN node-set get-log()
```

`get-log()` returns all [standard](/administration/logging.md#access-log) and [custom](/cookbook/custom-logging.md) log fields currently registered for access logging. It is mainly used to [test](/reference/testing/README.md) the [`log` action](/reference/actions/log.md).

## Example

```xml
<flat-test>
  <log>
  {
    "foo": "bar"
  }
  </log>
  <assert>
  [
    [ "get-log()/foo", "bar" ]
  ]
  </assert>
</flat-test>
```

## See also

* [Logging](/administration/logging.md) (Administration)
* [Custom Logging](/cookbook/custom-logging.md) (Cookbook)
* [`log` action](/reference/actions/log.md) (Reference)
