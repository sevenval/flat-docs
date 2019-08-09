# Flow

The flow describes how the incoming request from the client is transformed into the outgoing response.
The flow comprises [actions](actions.md), [control structures](#control-structures) and [variables](variables.md).

Here we have a flow with [`if-elseif-else` conditional statements](#if-elseif-else) and some [`echo` actions](actions/echo.md) evaluating the [`$request` variable](variables.md):

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

Each endpoint of your API can have its individual flow specified by `x-flat-flow` in the OpenAPI definition:

```yaml
…
paths:
  x-flat-flow: default.xml  # ⬅ fallback flow
  /:
    get:
      x-flat-flow: get.xml  # ⬅ flow for GET /
    post:
      x-flat-flow: post.xml # ⬅ flow for POST /
```

## Init Flow

An init flow is a flow that is executed before the regular flow for each path in the `basePath`.
It is specified by `x-flat-init` on the top level in the OpenAPI definition:

```yaml
…
x-flat-init: init.xml       # ⬅ init flow
paths:
  x-flat-flow: default.xml  # ⬅ fallback flow
  /:
    get:
      x-flat-flow: get.xml  # ⬅ flow for GET /
```

The init flow can e.g. be used to initialize variables or set HTTP response headers.

A `break` statement in the init flow terminates the **whole request**; the regular flow (specified by `x-flat-flow`) is **not** executed.
A `return` statement terminates only the **init flow**; the regular flow is executed.


## Control Structures

### `break`

`break` stops the current flow. Request processing will continue, though.
If used in a [sub flow](actions/sub-flow.md), all of its parent flows are halted, too.

A `break` statement should usually be executed conditionally, because otherwise
none of the following statements will ever be executed.

If specified in the init flow, `break` terminates the whole request.

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
A `return` statement on the top-most flow behaves like [`break`](#break).
