# `file-exists()`

```
boolean file-exists(string uriOrPath)
```

The `file-exists` function accepts exactly one argument that is converted to a string. The argument must be either a `file` or `fit` URI or a path to a local file. If the argument is a relative path, the path is resolved relative to the flow file's path.

The funktion returns `true` if the file exists, `false` otherwise.

If the argument is a URI with a URI scheme different from `file` or `fit`, the function throws an error.


## Examples

* `file-exists('relative/path/to/file')`
* `file-exists('../relative/path/to/file')`
* `file-exists('/absolute/path/to/file')`
* `file-exists('file:///absolute/path/to/file')`
* `file-exists('fit://site/conf/local.xml')`
