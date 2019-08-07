# Templating

FLAT's template language is intended to dynamically generate JSON documents.

Basically, you provide the skeleton of the JSON object you want to
create, and use _placeholders_ instead of literal values. The language supports _template commands_ that are documented below. _Template expressions_ and [functions](../functions/README.md) offer rich means of accessing request and response data in JSON, HTML or XML ([OXN](oxn.md)).

## [Comment `{{// <comment>}}`](comment.md)

## [Placeholder `{{ <expression> }}`](placeholder.md)

## [Dot `{{.}}`](dot.md)

## [Comma `{{,}}`](comma.md)

## [Pair Producer `{{: <expression> }}`](pair-producer.md)

## [Variable Definition `{{$<name> := <expression> }}`](variable.md)

## [Conditional `{{if <condition>}} … {{elseif <condition> }} … {{else}} … {{end}}`](if.md)

## [Loop `{{loop <array>}} … {{end}}`](loop.md)

## [Context Switch `{{with <expression>}} … {{else}} … {{end}}`](with.md)

## [Null Coalescing Operator `??`](null-coalescing-operator.md)
