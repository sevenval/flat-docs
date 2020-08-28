# `json-to-csv()`

```
string json-to-csv(OXN array)
```

The `json-to-csv` function translates the given [OXN `array`](/reference/templating/oxn.md) into CSV as
described in [RFC 4180](https://tools.ietf.org/html/rfc4180).
The array entries must either be arrays or "flat" objects with `number`, `boolean`, `string`, or `null` values.

If any errors occur, an empty `string` is returned.

Example: array of arrays

```xml
<flow>
  …
  <template out="$arr">
  [
    [ 1, "  foo ", true ],
    [ 2, "ba, r", false ],
    [ 3.21, "q\"u\"x", true ],
	[ '', null, '' ]
  ]
  </template>
  <eval out="$csv">json-to-csv($arr)</eval>
  <copy in="$csv"/>
</flow>
```

creates the following output:

```
1,  foo ,true
2,"ba, r",false
3.21,"q""u""x",true
,,
```


Example: array of "flat" objects

```xml
<flow>
  …
  <template out="$arr">
  [
    { "A": 1, "B": "  foo ", "C": true },
    { "A": 2, "B": "ba, r", "C": false },
    { "A": 3.21, "B": "q\"u\"x", "C": true },
    { "A": "", "B": null, "C": "" }
  ]
  </template>
  <eval out="$csv">json-to-csv($arr)</eval>
  <copy in="$csv"/>
</flow>
```

creates the same output.
