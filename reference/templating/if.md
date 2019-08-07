# Conditional ``{{if <condition>}} … {{elseif <condition> }} … {{else}} … {{end}}`


Conditional output.

```json
{{if <expression> }} … {{end}}

{{if <expression> }} … {{else}} … {{end}}

{{if <expression> }} … {{elseif <expression>}} … {{end}}

{{if <expression> }} … {{elseif <expression>}} … {{elseif <expression>}} … {{else}} … {{end}}

```

Input:
```json
{
  "a": -2,
  "b": 4,
  "c": 37,
  "issue": {
    "category": 6
  }
}
```

Template:
```json
{
  "numbers": [
    {{if a > 0 }} {{ b }} {{else}} {{ c }} {{end}}
  ],
  {{if issue/category }}
    "categorie": {{ issue/category }},
    {{if issue/category = 6 }}
      "is_private": false
    {{else}}
      "is_private": true
    {{end}}
  {{end}}
}
```

Output:
```json
{
  "numbers": [
    37
  ],
  "categorie": 6,
  "is_private": false
}
```
