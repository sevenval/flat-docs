# `regex` Action

## Syntax

Attributes:

* `pattern="..."` to define the search expression including delimiter and modifier (required)
* `replace="..."` to set the replacement text (required)
* `in="..."` to define the input location (optional, default is `fit://request/content`)
* `out="..."` to define the output location (optional, default is `fit://request/content`)

```xml
<regex pattern="#abc$#" replace="123" />
<regex in="$test" out="$test" pattern="/foo/i" replace="bar" />

```
## Usage

The `regex` action works on text content only. However, if the input is a DOM, it is
automatically serialized into a string for the action to work.
The output of this action is always a string.

The replacement is carried out with the [PCRE](https://www.pcre.org/) regular expression library.

## Example

This example converts a date of the form `YYYY-MM-DD` into `DD.MM.YY`:

```xml
<regex pattern="|20([012]\d)-(\d\d)-(\d\d)|" replace="$3.$2.$1"/>
```

## Errors

If the `pattern` does not compile, the request will terminate with an error.

It is not an error if the `pattern` does not match or the replacement is empty.
