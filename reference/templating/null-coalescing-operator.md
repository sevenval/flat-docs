# `??` Operator

Mit dem Null-Coalescing Operator `??` können Default-Werte implementiert werden.

Wenn der Ausdruck links vom Operator `null` zurück gibt, wird der Ausdruck rechts davon ausgeführt.

Der Operator kann in allen Template-Anweisungen verwendet werden, die Ausdrücke erlauben: Placeholder, if, elseif, Loops.

Input:
```json
{}
```

Template:
```json
{
    "name": {{ user/name ?? "unkown" }}
}
```

Output:
```json
{
  "name": "unknown"
}
```

Der Operator kann mehrfach in einer Template-Anweisung verwendet werden:

```json
{
  "name": {{ user/name ?? user/mail ?? "unknown" }}
}
```
