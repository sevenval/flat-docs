# `eval` Action

Evaluates its text content as an XPath expression. The result can be assigned to a
variable.

```xml
<eval out="$mock">false()</eval>
```

The action is mostly used to assign variables.

Besides atomic values such as strings, numbers and booleans, an expression may
yield a DOM node-set:

```xml
<eval out="$parts">split($request/path, "/")</eval>
```

```xml
<eval out="$links">content()/html/body//a[@href] | content()/html/head/link[@href]</eval>
```

If you need to create a structured JSON object use
[templating](/reference/templating/README.md).

## Syntax

The action body must contain an XPath expression.

`out` is optional. If defined, it must contain a variable name. If no `out` is
given, the result is discarded.

Omitting output is useful to call functions with side-effects:

```xml
<eval>fit-log(concat("login error for user ", $request/post/username))</eval>
```


## See also

* [Variables](/reference/variables.md)
* [`template`](template.md)
* [JSON Templating](/reference/templating/README.md)
