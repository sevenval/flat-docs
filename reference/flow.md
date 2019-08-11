# Flow

The flow describes how the incoming request from the client is transformed into the outgoing response.
The flow comprises [_actions_](actions.md), [control structures](#control-structures) and [variables](variables.md).

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

An _init flow_ is a separate flow file that is executed before the regular flow
that is defined for an API path. It is specified by setting `x-flat-init` on
the top level in the OpenAPI definition:

```yaml
…
x-flat-init: init.xml       # ⬅ init flow
paths:
  x-flat-flow: default.xml  # ⬅ fallback flow
  /:
    get:
      x-flat-flow: get.xml  # ⬅ flow for GET /
```

The init flow can be used [extract common initialization
tasks](/cookbook/init-flow.md), e.g. initialize variables or set HTTP response
headers.

For requests outside of the API `basePath` (e.g. `/` or `/assets`), the init
flow is _not_ executed. It is only called for API requests.

A [`break`](#break) statement in the init flow terminates the **whole
request**; the regular flow (specified by `x-flat-flow`) is _not_ executed. A
[`return`](#return) statement terminates only the init flow; the regular flow
is executed. Terminating [actions](actions/README.md) like
[`echo`](actions/echo.md) or [`dump`](actions/dump.md) will prevent the actual
flow being executed, too.


## Control Structures

### `break`

`break` stops the flow processing for the current request. It may be used in
[sub flows](actions/sub-flow.md) or the [init flow](#init-flow), too.

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
If `return` is used in the [init flow](#init-flow) the regular API path flow will still be executed.
A `return` statement on the top-most flow behaves like [`break`](#break).
