# `array-reverse()`

```
OXN-array array-reverse(OXN-array node-set)
```

given a JSON array in [Object XML Notation](/reference/templating/oxn.md), `array-reverse()` returns a new array in reverse order.

## Example

`json-stringify(array-reverse(json-parse('[2,1,3]')))` ➡ `[3,1,2]`
