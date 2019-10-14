# `xml-parse()`

```
node-set xml-parse(string xml)
```

The `xml-parse` function accepts exactly one argument. The value of the argument is converted to a string and parsed as an XML document. If the argument can be parsed as an XML document, the function returns a node-set containing the document node at the root of the resulting tree. Otherwise, an error is logged, and an empty node-set is returned.

## Example

```xml
<flow>
  <copy in="a.xml" out="$_xml"/>
  <eval out="$parsedXML1">xml-parse($_xml)</eval>
  <eval out="$parsedXML2"><![CDATA[xml-parse('<root foo="bar"><x/></root>')]]></eval>
  <template>
  {
    "firstItem": {{ $parsedXML1/list/item[1] }},
    "foo": {{ $parsedXML2/root/@foo }}
  }
  </template>
</flow>
```
