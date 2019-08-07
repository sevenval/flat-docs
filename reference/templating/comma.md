# `{{,}}`

Produces a literal comma (`,`) exactly if it is required for valid JSON.

## Usage

If conditional expressions follow each other, it can be hard to say whether a
comma is needed or not to obtain valid JSON:

```xml
<template>
{
  {{if maybe }}   "foo": "bar" {{end}} {{// place a comma here? }}
  {{if perhaps }} "bar": "baz" {{end}} {{// or here? }}
  {{: may-be-empty }}
}
</template>
```

If the first condition is `true`, a name/value pair will be produced. If and only if
the second condition is `true` as well, a comma will be needed to separate both pairs.
The same applies to the following
[pair producer `{{: …}}`](pair-producer.md) which may yield nothing.

To avoid having to repeat various combinations of the conditions to decide
whether or not to emit a comma, use the
comma command `{{,}}` to automatically produce all required commas:

```xml
<template>
{
  {{if maybe }}   "foo": "bar" {{end}} {{,}}
  {{if perhaps }} "bar": "baz" {{end}} {{,}}
  {{: may-be-empty }}
}
</template>
```

The `{{,}}` could be placed in the body of `{{if}}` as well:

```json
  {{if maybe }}   "foo": "bar" {{,}} {{end}}
  {{if perhaps }} "bar": "baz" {{,}} {{end}}
```

> 📎
> Note that the comma may appear after some whitespace in the output string.


The use of the `{{,}}` should be seen as a last resort, because it impairs the
template's readability. In many cases, the decision whether to place a comma or
not can be made a priori by reordering the name/value pairs,
producing _leading_ commas in conditionals or having a fixed
`{{else}}` block.

The [`{{loop}}` command](loop.md) produces commas between its productions, too. In loop bodies comma commands are allowed but usually not necessary.
