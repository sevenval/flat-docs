# `base64-decode()`

```xml
string base64-decode(string input [, boolean strict])
```

Dekodiert die in dem `input` Parameter übergebenen base64-kodierte Daten.

Ist der optionale `strict` Parameter gleich `true()`, dann gibt die `base64-decode()`
Funktion `false()` zurück, wenn die Eingabe Zeichen enthält, die nicht im Base64-Alphabet
vorkommen. Andernfalls werden ungültige Zeichen stillschweigend ausgesondert.
