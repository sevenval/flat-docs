# `file-exists()`

```
boolean file-exists(string path)
```

The `file-exists` function accepts exactly one argument that is converted to a string. The argument must be a `fit://`-URL, otherwise an empty result is returned and an error will be raised.

The function returns `true` if the URL references an existing file or directory, `false` otherwise.


## Examples

* `file-exists('fit://site/path/to/file')`
* `file-exists('fit://site/path/to/directory/')`
* `file-exists('fit://request/content')`