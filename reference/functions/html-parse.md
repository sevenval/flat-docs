# `html-parse()`

The `html-parse` function accepts exactly one argument that is converted to a string. The argument is parsed as an HTML document. The function returns a DOMNodeList containing the document node at the root of the resulting tree.

## Example

```xml
<flow>
  <copy in="a.html" out="$html"/>
  <eval our="$parsedHTML">html-parse($html)</eval>
  <template>
  {
    "firstP": {{ $parsedHTML/html/body/main/p[1] }}
  }
  </template>
</flow>
```
