# `log` Action

Used to augment the [access log](/administration/logging.md#access-log) with [custom fields](/cookbook/custom-logging.md).

## Syntax

```xml
<log>
{
  "field1": "value1",
  "field2": "value2"
}
</log>
```

The action takes a JSON object as its argument. The object may contain nested fields. A [JSON template](/reference/templating/README.md) may be used for dynamic fields. (The [recipe](/cookbook/custom-logging.md) has plenty examples for that.)

## Usage

All name/value pairs of the object are registered for logging.  When the system writes the `flat_access` event the registered fields are included in that log line.

[System log fields](/administration/logging.md#fields) like `timestamp` cannot be overriden.

You can call the action multiple times. Fields of the same name are overwritten. However, nested fields are merged into the previously registered log fields.
The order of fields is maintained.

```xml
<log>
{
  "user": {
    "name": "alice"
  }
}
</log>
<log>
{
  "user": {
    "role": "admin"
  }
}
</log>
```

The merged custom log fields are:
```json
{
  "user": {
    "name": "alice",
    "role": "admin"
  }
}
```

They would appear in the [access log](/administration/logging.md#access-log) like this:

```json
{"timestamp": …,"user":{"name":"alice","role":"admin"}}
```

Fields with `null` values are not included in the log event. In order to remove a previously registered field, you can unset it with `null` value.

The `user` field of the previous example can be unregistered like this:
```xml
<log>
{
  "user": null
}
</log>
```

## See also

* [Logging](/administration/logging.md) (Administration)
* [Custom Logging](/cookbook/custom-logging.md) (Cookbook)
* [`get-log()` function](/reference/functions/get-log.md) (Reference)
