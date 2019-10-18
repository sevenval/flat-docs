# `html-parse()`

```
node-set html-parse(string html)
```

The `html-parse` function accepts exactly one argument. The value of the argument is converted to a string and parsed as an HTML document. The function returns a node-set containing the document node at the root of the resulting tree.

## Example

```xml
<flow>
  <copy in="a.html" out="$html"/>
  <eval out="$parsedHTML">html-parse($html)</eval>
  <template>
  {
    "firstP": {{ $parsedHTML/html/body/main/p[1] }}
  }
  </template>
</flow>
```
