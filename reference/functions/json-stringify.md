# `json-stringify()`

```
string json-stringify(OXN json, bool pretty = false())
```

Serializes a JSON ([OXN](/reference/templating/oxn.md)) object or an atomic value (string, number, boolean) into JSON text.

The compact (unpretty) form (default) is useful for logging as it contains no line breaks.

## Parameters

* `json`: A JSON object/array or atomic value
* `pretty`: A boolean indicating compact or pretty formatting

## Examples

* `json-stringify(true())` → `true`
* `json-stringify(42)` → `42`
* `json-stringify("foo")` → `"foo"`
* `json-stringify($data)` → `{"foo":"bar"}`
* `json-stringify($data, true())` →
  ```
  {
    "foo": "bar"`
  }
  ```
* `json-stringify(json-parse('[1, 2]'))` → `[1, 2]`

## See also

* [`json-parse()`](/reference/functions/json-parse.md)
* [`assert`](/reference/actions/assert.md)
