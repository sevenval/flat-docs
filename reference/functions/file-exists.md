# `file-exists()`

```
boolean file-exists(string path)
```

The `file-exists` function accepts exactly one argument that is converted to a string. The argument must be a path to a local file. If the argument is a relative path, the path is resolved relative to the flow file's path.

The funktion returns `true` if the file exists, `false` otherwise.


## Examples

* `file-exists('relative/path/to/file')`
* `file-exists('../relative/path/to/file')`
* `file-exists('/absolute/path/to/file')`
