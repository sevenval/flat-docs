# `array-reverse()`

```
OXN-array array-reverse(OXN-array node-set)
```

Reverses the given JSON array in [OXN](/reference/templating/oxn.md).

## Example

`json-stringify(array-reverse(json-parse('[1,2,3]')))` ➡ `[3,2,1]`
