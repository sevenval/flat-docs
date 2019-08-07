# `with`

Der Template-Befehl `{{with _expression_}}` setzt den Kontext für alle nachfolgenden Ausdrücke auf das Ergebnis von `expression`, oder führt den zugehörigen `{{else}}`-Teil aus, wenn das Ergebnis `null` ist.

Input:
```json
{
  "user": {
    "name": "alice",
  }
}
```

Template:
```xml
<template>
{
  {{with ./user}}
    "Name": {{name}}
  {{else}}
    "Name": "unknown"
  {{end}}
}
</template>
```

Output:
```json
{
  "Name": "alice"
}
```

### Beispiel: `with` und `json-doc()`

`{{with}}` ist insbesondere zusammen mit der `json-doc()` Funktion sinnvoll, um
Daten aus verschiedenen Dateien ohne viel Schreibarbeit zusammenzuführen.

Input:
```json
{
  "location": "home"
}
```

Input `fit://request/request/body`:
```json
{
  "user": "fred",
  "pass": "wilma"
}
```

Template:
```xml
<template>
{
	"realm": {{ location }},
	{{with json-doc("fit://request/request/body") }}
	  "user": {{ user }},
	  "password": {{ pass }}
	{{end}}
}
</template>
```

Output:
```json
{
  "realm": "home",
  "user": "wilma",
  "password": "fred"
}
```
