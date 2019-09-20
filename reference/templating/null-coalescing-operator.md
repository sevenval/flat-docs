# `??` Operator

The null coalescing operator `??` can be used to return default values.

If the expression left of the `??` operator evaluates to `null`, the expression to the right of the operator is evaluated.

The operator can be used in all templating commands that allow expressions, such as [placeholder](placeholder.md), [if](if.md), [elseif](if.md) or [loops](loop.md).

Input:
```json
{}
```

Template:
```json
{
    "name": {{ user/name ?? "unkown" }}
}
```

Output:
```json
{
  "name": "unknown"
}
```

The `??` operator can be used multiple times in a template expression:

```json
{
  "name": {{ user/name ?? user/mail ?? "unknown" }}
}
```


> 📎
> Note that `??` operator(s) are used to split the template expression into subexpressions and therefore cannot be used in arguments of functions within those subexpressions.
