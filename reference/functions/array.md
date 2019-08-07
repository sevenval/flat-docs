# `array()`

```
node-set array([node-set nodes])
```

verpackt die Knoten des Node-sets `nodes` in eine `<json array="">…</json>`-Struktur, so dass sich die einzelnen Knoten in einem [JSON Template](../Templating/README.md) wie ein Array verarbeiten lassen.

Wenn im Node-set ein Attribut (`a="b"`) übergeben wird, wird es in ein Element (`<value name="a">b</value>`) verpackt, damit Key und Value im Template zugänglich sind.

## Beispiel

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

Ausgabe:
```json
{ "names": ["Alice","Bob"] }
```
