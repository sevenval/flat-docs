# `split()`

```
node-set split(string subject, [string pattern])
```

`split()` converts the "subject" string into a node-set of `<token>` elements.
The optional pattern is a Perl-compatible regular expression (without
delimiters). It is used to split the input string. If no pattern is given, the
string is split at every block of whitespace characters (space, tab, newline).

Example without pattern:
```
split("A B C")
```
returns
```xml
<token>A</token>
<token>B</token>
<token>C</token>
```

Example with pattern
```
split("A:B : C", "\s*:\s*")
```
returns
```xml
<token>A</token>
<token>B</token>
<token>C</token>
```

The elements of the result node-set may be used with positional predicates:
```
split("A B C")[2]
```
returns
```xml
<token>B</token>
```

For use in a [Template](../Templating/README.md) `loop`, the result node-set
has to be casted into an array with the [`array()` function](#array):

```xml
<template>
[
  {{loop array(split("Alice.Bob.Eve", "\.")) }}
  {{.}}
  {{end}}
]
</template>
```

If necessary, in-place regex modifiers can be used with the `(?<modifier>)` syntax, where `<modifier>` can be
* `i`: case-insensitive match
* `m`: `^` and `$` match for every line in the subject ("multiline")
* `x`: ignore whitespace in pattern for readability ("extended")

Example with case-insensitive match of `x` and `X`:
```xpath
split("AXBxC", "(?i)x")
```
returns
```xml
<token>A</token>
<token>B</token>
<token>C</token>
```
