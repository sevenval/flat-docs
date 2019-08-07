# `sub-flow` Action

To better organize or reuse your [flow](../flow.md) code, you can split it into separate files.
The `sub-flow` action executes the flow file referenced in its `src="…"` attribute.

## Usage

Normally, a sub flow runs until all its instruction have been executed. Then,
control is handed over to the parent flow again.

You can return at any point in your sub flow with a (usually conditional) [`<return/>` statement](../flow.md#return).

The [`<break/>` statement](../flow.md#break) terminates all flows. The parent flow will not be
continued. Terminating actions like [`dump`](dump.md) or
[`echo`](echo.md) terminate the whole request, too.

## Example

```xml
<flow>
  <sub-flow src="my-sub-flow.xml"/>
  …
</flow>
```

`my-sub-flow.xml`:
```xml
<flow>
  <if test="…">
    <sub-flow src="my-sub-sub-flow.xml"/>
    <return/>
  </if>
  …
</flow>
```

The `src` file is resolved relatively to the calling flow file.
