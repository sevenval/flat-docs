# Pair Producer `{{: …}}`

The pair producer `{{: expression_selecting_keys }}` creates key-value pairs. It should only be used in a context where key-value pairs are allowed (i.e. in a JSON object context).

The expression following the colon (':') is evaluated and the result is used as a list of keys. All key-value pairs in the current context with a matching key in this list of keys are copied to the output.

**Note**: With markup input, you can only select element or attribute nodes, because other node types don't have a name (see "Example: XML input").

The pair producer also creates commas between _and after_ its pairs. Therefore
you should avoid placing an explicit comma behind a pair producer. If an
expression does not match anything, no output would be produced. In that case,
your literal comma could be illegal.

Example: explicit keys

Input:
```json
{
  "foo": 1,
  "bar": [{"key":"value"}],
  "baz": 3
}
```
Template:
```
{
  {{: foo | bar }}
}
```
Output:
```json
{
  "foo": 1,
  "bar": [{"key":"value"}]
}
```

Example: filter

Input:
```json
{
  "foo": 1,
  "bar": 2,
  "ba z": {"key":"value"}
}
```
Template:
```
{
  {{: *[not(name() = 'bar')] }}
}
```
Output:
```json
{
  "foo": 1,
  "ba z": {"key":"value"}
}
```

Example: automatic comma

Input:
```json
{
  "foo": 1,
  "bar": 2,
  "baz": 3
}
```
Template:
```
{
  {{: * }}
  "add": "this"
}
```
Output:
```json
{
  "foo": 1,
  "bar": 2,
  "baz": 3,
  "add": "this"
}
```

Example: XML input

Input:
```xml
<root foo="1">
  <bar>2</bar>
  <baz>3</baz>
  <!-- a comment -->
</foo>
```
Template:
```
{
  {{: @foo | //bar | //baz/text() | //comment() }}
}
```
Output:
```json
{
  "foo": 1,
  "bar": 2
}
```
