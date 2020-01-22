# `verify-xmldsig()`

```
boolean verify-xmldsig(element)
```

Verifies the digital signature ([XMLDSig](https://en.wikipedia.org/wiki/XML_Signature))
of an XML element.

`element` is expected to be a node-set with the signed element as its first node, containing the `Signature` element
in the `http://www.w3.org/2000/09/xmldsig#` namespace, or a signed DOM document or an XML string.
Only the first node of a provided node-set will be verified.

Returns `true()` if the included XML `Signature` element is valid for the given element, `false()` otherwise.

## Example

```
<eval out="$valid">verify-xmldsig($signed-xml)</eval>
```
