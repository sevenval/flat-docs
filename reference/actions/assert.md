# `assert` Action

Used in [FLAT tests](/reference/testing/README.md) to assert expected results.

## Syntax

```xml
<assert>
[
  [ "$status", 200, "status is 200" ]
]
</assert>
```

The body is a JSON array of assertions. An assertion is an array itself, with up to three values:

* Expression: _as string_ (see [`eval`](/reference/actions/eval.md)); (required)
* Expected result: a literal value (string, number, boolean, or `null`) or an object with compare flags (see below); (optional, default: `true`)
* Message: String literal to be included in output of failed tests; (optional)

Note that the expression must be a *string*. You can use template syntax, but the resulting array of assertions must still contain a string as the expression parameter.

## Examples

```xml
<assert>
[
  [ "$foo", "bar", "…" ],
  [ "$request/headers/authorization", null, "No Authorization header is set" ],
  [ "$response", "OK" ],
  [ "json-parse($response)/user", "alice" ],
  [ "$status", 201, "got 201 - created" ],
  [ "true()", true, "for sure!" ]
]
</assert>
```

## Compare Flags

The expected value (second value) can be an object that defines one or more _compare flags_:

* `file`: read wanted text from a _golden file_
* `mode`: either `text` or `json`. `json` mode  validates JSON syntax in both expected and actual result; compares JSON in compact (un-pretty) formatting.
* `contains`: a string that must be contained in the expression result
* `pattern`: a regular expression pattern (with delimiters and optional modifiers) that the expression result must match.

```xml
<assert>
[
  [ "$response", {"file": "login.golden", "mode": "json"} ]
]
</assert>

<assert>
[
  [ "$s1", {"contains": "foo bar"} ],
  [ "$s2", {"pattern": "#^[a-z ]+$#i"} ]
]
</assert>
```

The [Testing Templates](/cookbook/test-templates.md) recipe provides a full example.

## See also

* [Testing Templates](/cookbook/test-templates.md) (cookbook)
* [Testing Upstream Requests](/cookbook/test-backend.md) (cookbook)
* [Testing](/reference/testing/README.md) (reference)
* [`json-parse()`](/reference/functions/json-parse.md)
* [`json-stringify()`](/reference/functions/json-stringify.md)
