# `json-doc()`

```
node-set json-doc(string path)
```

converts the JSON document at the given `path` into
[OXN](../templating/oxn.md), parses it and
returns the `<json>` _root element_.

Relative paths are resolved relative to the flow file's path.

`user.json`:
```json
{
  "name": "brenda"
}
```

```
json-doc("user.json")/name
```

## Example: Read a JSON config

Flow:

```xml
<flow>
  <echo if="json-doc('fit://site/conf.json')/maintenance" status="503">
    Under maintenance
  </echo>
</flow>
```
