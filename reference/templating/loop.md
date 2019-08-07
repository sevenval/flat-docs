# `loop`

Input:
```json
{
  "list": [
    1,
    2,
    3
  ]
}
```

Template:
```xml
<template>
{
  "numbers": [
    {{loop list}}
      {{ . }}
    {{end}}
  ]
}
</template>
```

Output:
```json
{
  "numbers": [
    1,
    2,
    3
  ]
}
```

Die `loop`-Anweisung arbeitet nur auf Arrays. Der Ausdruck muss also einen
Array-Typ zurückgeben. Der `loop`-Body wird für jedes Element des Arrays aufgerufen.
Dabei wird der Kontext `.` auf dieses Element gesetzt.
Anstelle der Schreibweise für Placeholder mit führendem Leerraum `{{ . }}` ist
hier auch alternativ das kürzere `{{.}}` zulässig.

In the above example, we did not explicitly placed a comma in the loop body. But JSON
requires a comma to separate array values or pairs in objects. Therefore, the `loop`
command _automatically_ produces commas to separate its child productions.

A literal comma may be used at the end of the loop body, too. This will not result in
double commas. But as a literal comma is part of each iteration, the last production
will end with that comma, too. This may be wrong if the surrounding array/object ends
after the loop.


### Beispiel: loop mit nicht-JSON (HTML)

Wenn über Daten iteriert werden soll, die nicht als JSON-Array vorliegen, kann
die `array()` Funktion zum Konvertieren verwendet werden:

Input:

```html
<html>
<body>
<ul id="list" foo="bar">
  <li>Eins</li>
  <li>Zwei</li>
  <li>Drei</li>
</ul>
</body>
</html>
```

Template:
```xml
<template>
[
  {{loop array(/html/body/ul/li)}}
    {{.}}
  {{end}}
]
</template>
```

Output:
```json
[
  "Eins",
  "Zwei",
  "Drei"
]
```

Wenn `array()` ein Attribut übergeben bekommt, wird es in ein Element
umgewandelt, das den Attribut-Namen im `name`-Attribut und den Wert als Inhalt
hält. So kann man z.B. über eine Attribut-Liste iterieren:

Template (bei gleichem Input):
```xml
<template>
{
  {{loop array(//ul/@*)}}
    {{@name}}: {{.}}
  {{end}}
}
</template>
```

Output:
```json
{
  "id": "list",
  "foo": "bar"
}
```
