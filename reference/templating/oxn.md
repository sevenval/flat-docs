# Object XML Notation (OXN)

Internally, decoded JSON is represented in _Object XML Notation_ (OXN). This allows [templates](/reference/templating/README.md) to uniformly address data in JSON, XML and HTML documents with XPath expressions.

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

Under most circumstances, explicit conversions between both formats are not necessary.

## See also

* [`json-stringify()`](/reference/functions/json-stringify.md)
* [`json-parse()`](/reference/functions/json-parse.md)
* [JSON Templates](/reference/templating/README.md)
