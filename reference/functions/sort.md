# `sort()`

```
OXN-array sort(OXN-array node-set, string key = '', boolean descending = false())
```

Given a JSON array in [Object XML Notation](/reference/templating/oxn.md), `sort()` returns a new, sorted array. The optional `key` expression specifies the sorting key. An empty string is ignored. The default behaviour is to sort in ascending order. To sort in descending order, the optional third parameter must be `true()`.

## Examples

* `json-stringify(sort(json-parse("[2,1,3]")))` ➡ `[1,2,3]`
* `json-stringify(sort(json-parse("[2,1,3]", "", true())))` ➡ `[3,2,1]`
* with `$data` being `[{"id":3,"name":"A"},{"id":1,"name":"C"},{"id":2,"name":"B"}]`:
  * `json-stringify(sort($data, "id"))` ➡ `[{"id":1,…},{"id":2,…},{"id":3,…}]`
  * `json-stringify(sort($data, "id", true()))` ➡ `[{"id":3,…},{"id":2,…},{"id":1,…}]`
  * `json-stringify(sort($data, "name"))` ➡ `[{…"A"},{…"B"},{…"C"}]`
