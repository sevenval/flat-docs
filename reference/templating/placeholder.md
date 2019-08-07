# Placeholder


Die Adressierung von Werten aus JSON-Objekten erfolgt über mit Slash `/` getrennten Property-Namen.


Achtung: Bei `placeholder` ist führender Leerraum
erforderlich, alle anderen Befehle werden ausschließlich in der oben angegebenen
Schreibweise erkannt!

### Beispiel: Placeholder

Input:
```json
{
  "account": {
    "name": "alice",
    "mail": "alice@example.com",
    "id": 4711
  }
}
```

Template:
```xml
<template>
{
  "user": {{ account/name}},
  "id": {{ account/id }},
  "admin": {{ account/id = 0 }}
}
</template>
```

Ergebnis:
```json
{
  "user": "alice",
  "id": 4711,
  "admin": false
}
```

Der Placeholder erzeugt eine typisierte Ausgabe. Es dürfen z.B keine Quotes (`""`) um den `{{ … }}` Ausdruck geschrieben werden. Das geschieht bei der Ausführung des Templates automatisch. So können alle JSON-Typen, auch Zahlen, Boolesche Werte oder Arrays und Objekte korrekt produziert werden.
