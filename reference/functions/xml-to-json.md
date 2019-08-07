# `xml-to-json()`

```
string xml-to-json(node-set node)
```

`xml-to-json` translates the given `node` into its JSON
representation. The structure of the `node` must be in [Object XML Notation](../templating/oxn.md).
`xml-to-json` expects exactly one single node as parameter. In cases of errors, an empty `string`
is returned.
