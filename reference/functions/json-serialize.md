# `json-serialize()`

```
string json-serialize(OXN json, bool pretty = false)
```

Serializes a JSON ([OXN](/reference/templating/oxn.md)) object or atomic values (strings, numbers, booleans) into JSON text.

The compact form (default) is useful for logging.

## Parameters

* `json`: A JSON object/array or atomic value
* `pretty`: A boolean indicating compact or pretty formatting

## Example

```
json-serialize(true) = 'true'
json-serialize("foo") = '"foo"'
json-serialize($data) = '{"foo":"bar"}'
json-serialize($data, true) = '{
  "foo": "bar"
}'
json-serialize(json-parse('[1, 2]')) = '[1, 2]'
```

## See also

* [`json-parse()`](/reference/functions/json-parse.md)
* [`assert`](/reference/actions/assert.md)
