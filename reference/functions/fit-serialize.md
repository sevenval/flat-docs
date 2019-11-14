# `fit-serialize()`

```
string fit-serialize(string|number|boolean|node-set argument [, boolean pretty-print = true()])
```

The XPath function `fit-serialize` generates a storable and human readable representation
of the given node-set. For example, you could use this function to obtain a string
representation of an XML DOM and log it via the [`fit-log()`](fit-log.md) function
for debugging.

If the type of the `argument` is scalar (`string`, `number` or `boolean`), the `argument` is cast to
`string` and output as is.

If the `pretty-print` parameter is set to `true()` (which is the default), the `fit-serialize` function will
try to generate nicely formatted output for a `node-set`. If set to `false()` the `argument` is output as is.

An empty `node-set` results in an empty `string`.

## Examples

```markup
<flow>
  <default-request />
  ...
  <dump if="fit-log(fit-serialize(server))" />
  ...
</flow>
```

Example output of the `server` Delivery Context properties
in the `fit_engine.log`:

```
[23-Mar-2017 06:56:33 UTC] WNNxoX8AAAEAAAMKKZQAAACW:myProject <server>
  <fit-version>14.6.3</fit-version>
  <project>myProject</project>
  <role>production</role>
  <site>mySite</site>
  <ucm>3dd4288fa57c4d192785d3dda3b05a73</ucm>
</server>
```


Using XSLT:

```xml
...
  <xsl:variable name="myVar">
    <foo>
      <bar>value</bar>
    </foo>
  </xsl:variable>
...
  <xsl:value-of select="fit-log(fit-serialize($myVar))" />
...
```

Example output in the `fit_engine.log`:

```
[27-Mar-2017 09:06:54 UTC] WNNxoX8AAAEAAAMKKZQAAACW:myProject  <?xml version="1.0"?>
<foo>
  <bar>value</bar>
</foo>
```
