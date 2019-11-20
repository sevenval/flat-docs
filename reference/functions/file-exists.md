# `file-exists()`

```
boolean file-exists(string path)
```

The `file-exists` function accepts exactly one argument that is converted to a string. The argument must be a `fit://`-URL, otherwise an empty result is returned and an error will be raised.

The function returns `true` if the URL references an existing file or directory, `false` otherwise.


## Examples

* `file-exists('relative/path/to/file')`
* `file-exists('../relative/path/to/file')`
* `file-exists('/absolute/path/to/file')`
