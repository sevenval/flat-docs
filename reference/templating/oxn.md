# Object XML Notation (OXN)

Templating data can not only be represented as JSON but also as Object XML Notation (OXN).

The following JSON and OXN are equivalent.

```json
{
  "Object": {
    "String": "foo",
    "Number": -1234.56,
    "Boolean": true,
    "Null": null,
    "Array": ["bar", 42]
  },
  "Empty Object": {}
}
```

```xml
<json object="">
  <Object object="">
    <String string="">foo</String>
    <Number number="">-1234.56</Number>
    <Boolean boolean="">true</Boolean>
    <Null null=""/>
    <Array array="">
      <value string="">bar</value>
      <value number="">42</value>
    </Array>
  </Object>
  <json-element name="Empty Object" object=""/>
</json>
```

See also
* [`json-to-xml()`](../functions/json-to-xml.md)
* [`xml-to-json()`](../functions/xml-to-json.md)
