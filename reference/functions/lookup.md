# `lookup()`

```
string lookup(string fidj-file-uri, string key [, string default = ""])
```

The XPath function `lookup` returns the value associated with the `key`
in a database file. The database file specified by the `fidj-uri` can be either in JSON
or [SHMT](https://github.com/sevenval/SHMT) format. If the
`key` is not found, the `default` string will be returned.

* `fidj-file-uri`: [FIDJ URI](../config/FS_Structure.html#fit-protocol) pointing to a local database _file_. The extension
  of the database file must be either lowercase `.json` for a JSON file or lowercase
  `.shmt` for an SHMT file.
* `key`: database key to search for
* `default`: optional value to return if the
  `key` is not found in the database (default is an empty string `""`)


## Using a JSON file

The JSON DB file has to be implemented as a key-value object (SHMT
does this automatically):

```javascript
{
  "key1": "value1",
  "key2": [1, 2, 3],
  "key3": {"k":"v"}
  ...
}
```

;;block-tip

If a value is either of type `object` or `ordered list of values` (`array`),
the value is always converted to a JSON string. By using the [`json-to-xml()`](XPath_Functions.html#json-to-xml)
function, the result string can be translated into a corresponding XML structure.

Example:

```xml
  <xsl:value-of select="lookup('fit://site/databases/db.json', 'key3')" />
```

would result in the string `"{\"k\":\"v\"}"`.

block;;


## Debugging

The `lookup` XPath function provides many information or error messages in
the `json` [debug channel](../core/Debugging.html).


## Examples

```xml
...
  <xsl:value-of select="lookup('fit://project/db/my.json', 'full_name', 'John Doe')" />
...
  <xsl:value-of select="lookup('fit://site/databases/db.shmt', 'key')" />
...
```
