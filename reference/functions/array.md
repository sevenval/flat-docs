# `array()`

```
node-set array([node-set nodes])
```

wraps the nodes of the node set `nodes` in a `<json array="">…</json>` structure, so that they can be used as an array in a [JSON Template](../templating/README.md).

if the `nodes` contain an attribute (e.g. `a="b"`), it will be converted to an element `<value name="a">b</value>`), so that key and value are easily accessible in the template.

## Example

JSON:
```json
{"users": [ {"name": "Alice"},{"name": "Bob"}]}
```

Template-Action:
```xml
<template>
   { "names": {{ array(users//name) }} }
</template>
```

Output:
```json
{ "names": ["Alice","Bob"] }
```
