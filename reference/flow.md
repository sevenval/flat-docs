# Flow

The flow describes how the incoming request from the client is transformed into the outgoing response.
The flow comprises [_actions_](actions/README.md), [control structures](#control-structures) and [variables](variables.md).

Each endpoint of your API can have its individual flow specified by `x-flat-flow`, see [Routing](OpenAPI/routing.md).

Here we have a flow with [`if-elseif-else` conditional statements](#less-than-if-greater-than-less-than-elseif-greater-than-less-than-else-greater-than) and some [`echo` actions](actions/echo.md) evaluating the [`$request` variable](variables.md):

```xml
<flow>
  <if test="$request/query = 42">
    <echo>Yeah, that's it!</echo>
  </if>
  <elseif test="$request/query">
    <echo>Um, no!</echo>
  </elseif>
  <else>
    <echo>Do you know the answer?</echo>
  </else>
</flow>
```



## Control Structures

### `break`

`break` stops the flow processing for the current request. It may be used in
[sub flows](actions/sub-flow.md) or the [init flow](OpenAPI/routing.md#init-flow), too.

Response processing (such as validation and sending out the response) will
continue, though.

A `break` statement should usually be executed conditionally, because otherwise
none of the following statements will ever be executed.


```xml
<flow>
  <break/>
  <echo>will never be reached</echo>
</flow>
```

See also:
 * [`echo`](actions/echo.md)
 * [`dump`](actions/dump.md)
 * [`return`](#return)
 * [`sub-flow`](actions/sub-flow.md)


### `<if>`-`<elseif>`-`<else>`

The `if` statement and the optional `elseif` and `else` statements allow for conditional execution of flow blocks.

```xml
<flow>
  <if test="…">…</if>

  <if test="…">…</if>
  <else>…</else>

  <if test="…">…</if>
  <elseif test="…">…</elseif>

  <if test="…">…</if>
  <elseif test="…">…</elseif>
  <elseif test="…">…</elseif>
  <else>…</else>
</flow>
```

Conditional expressions are defined in the `test="…"` attributes of the `if` and `elseif` statements.
If such an expression evaluates to `true`, the flow block inside that `if` (or `elseif`) will be executed. All directly following `elseif` and `else` blocks will then be skipped.

If the result of that expression is `false`, the block will be skipped and the condition of the following `elseif` statement will be checked, if applicable.

The block associated with the first matching conditional expression will be executed – or if all  expressions were evaluated to `false`, the `else` block will be executed.

### `return`

`return` quits the current [sub flow](actions/sub-flow.md) and returns to its parent flow.
If `return` is used in the [init flow](OpenAPI/routing.md#init-flow) the regular API path flow will still be executed.
A `return` statement on the top-most flow behaves like [`break`](#break).
