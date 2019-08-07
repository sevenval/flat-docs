# `fit-document()`

The `fit-document` function accepts exactly one argument that is converted to a string. The argument must be an URI pointing to a `fit://`-URL, otherwise an empty result is returned and an error will be raised. The resource referenced by that URL is retrieved and the data resulting from the retrieval action is parsed as an XML document and a tree is constructed in accordance with the data model.

If the content pointed to by the URI can be retrieved and parsed, the `fit-document` function returns a DOMNodeList containing the document node at the root of the resulting tree. When attempting to access non-existent content, the DOMNodeList will be empty.

The `fit-document` function is similar to the  [XSLT document function](http://www.w3.org/TR/xslt#document).

## Example

```xml
<flow>
  <dump if="fit-document('fit://site/conf/local.xml')/conf/dump" />
</flow>
```
