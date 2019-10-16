# `sort()`

```
OXN-array sort(OXN-array node-set, string key = '', boolean descending = false())
```

Sorts the given JSON array in [OXN](/reference/templating/oxn.md). The optional `key` expression specifies the sorting key. An empty string is ignored. To sort descending, the optional third parameter must be `true()`.

## Examples

* `json-stringify(sort(json-parse("[2,1,3]")))` ➡ `[1,2,3]`
* `json-stringify(sort(json-parse("[2,1,3]", "", true())))` ➡ `[3,2,1]`
* with `$data` being `[{"id":3,"name":"A"},{"id":1,"name":"C"},{"id":2,"name":"B"}]`:
  * `json-stringify(sort($data, "id"))` ➡ `[{"id":1,…},{"id":2,…},{"id":3,…}]`
  * `json-stringify(sort($data, "id", true()))` ➡ `[{"id":3,…},{"id":2,…},{"id":1,…}]`
  * `json-stringify(sort($data, "name"))` ➡ `[{…"A"},{…"B"},{…"C"}]`
