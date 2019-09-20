### Template Variables

Local variables can be set as follows

```json
{{$variable := expr}}
```

where `variable` is a freely chosen name and `expr` is a template expression.

Note that we use the "beaver operator" `:=` for assignments. A simple `=` is
the "equal operator". Additionally, there may be no whitespace between the
`{{`, the `$` and the name of the variable.

The name of the variable must begin with a letter or underscore, followed by an
arbitrary sequence of letters, numbers and underscores.

The visibility of the variable is limited to the current template. Global
variables (e.g. `$env`) may be overridden locally in a template without
affecting its global value.

Local variables can hold strings, numbers, booleans, JSON Objects and arrays or
DOM node-sets. Variables can be reassigned.

```json
{{$i := 0 }}
{{$i := $i + 1 }}
{{$i := "Hey" }}
{{$i := concat('https://', $env/UPSTREAM_HOST) }}
{{$i := //*/@src }}
```

After a value has been assigned to a variable, it can be used in any
expression.
