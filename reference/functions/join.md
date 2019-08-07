# `join()`

```
string join(glue, input1, input2 …)
```

`join()` returns a string that contains all given input arguments with the
`glue` string between them.

The function is variadic. It accepts an arbitrary number of arguments. The
second and following arguments are used as input.

Example with basic types:
```
join(":", "a", "b")
```
returns `a:b`.

The input can be a string or a number or a node-set. In the latter case, all
nodes in the node-set are casted into a string.

Example XML input:

```xml
<names>
  <n>Alice</n>
  <n>Bob</n>
</names>
```

Call with node-set:
```
join(", ", names/*)
```
returns `Alice, Bob`.


Example JSON input:

```json
{
  "names": [
	"Alice",
	"Bob"
  ]
}
```

If an argument is a JSON array its values will be joined:

```
join(", ", names)
```
returns `Alice, Bob`.
