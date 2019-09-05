# `json-parse()`

```
OXN-node-set json-parse(string json)
```

Parses a JSON text into a structured JSON document ([OXN](/reference/templating/oxn.md)) and returns its root node.

## Parameters

* `json`: A valid JSON in serialized form (string).

If the parameter cannot be decoded, the function returns an empty node-set.

## Example

```
json-parse('{"active":true}')/active = true()
json-parse($data/description)
json-parse(json-stringify($body))
```

## See also

* [`json-stringify()`](/reference/functions/json-stringify.md)
* [`assert`](/reference/actions/assert.md)
