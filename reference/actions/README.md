# Flow Actions

* [`auth`](auth.md)
* [`copy`](copy.md)
* [`debug`](debug.md)
* [`dump`](dump.md)
* [`echo`](echo.md)
* [`nameshave`](nameshave.md)
* [`regex`](regex.md)
* [`request`](request.md)
* [`requests`](requests.md)
* [`serve`](serve.md)
* [`set-config`](set-config.md)
* [`set-response-headers`](set-response-headers.md)
* [`set-status`](set-status.md)
* [`sub-flow`](sub-flow.md)
* [`template`](template.md)
* [`xslt`](xslt.md)


## `debug` Attribute

In FLAT, every flow action can have a `debug` attribute. Its value is a
comma-separated list of log topics:

```
debug="topic1, topic2"
```

Usually actions can be debugged by type, for example to debug all `request` or
`template` actions. _Debug topics_ allow you to selectively filter debug
output of one or more specific actions.

See [Selective Action Debugging](../debugging.md#selective-action-debugging)
for more information.

```xml
<flow>
  <template>{"v": 0}</template>
  <template debug="overrides">{"v": 1}</template>
  <template debug="overrides, special-topic">{"v": 2}</template>
</flow>
```

* The debug topic `template` yields output of all three `template` actions.
* The debug topic `overrides` yields output of the two latter actions.
* The debug topic `special-topic` yields output of only the last action.


Specifying `debug` on a `sub-flow` action, automatically applies the debug topic to all
actions in that sub-flow.

```xml
<flow>
  <template>{"v": 1}</template>
  <sub-flow src="sub.xml" debug="foo"/>
</flow>
```

`sub.xml`:

```xml
<flow>
  <template>{"v": 2}</template>
  <template debug="bar">{"v": 3}</template>
</flow>
```

* The debug topic `foo` yields output of all actions in `sub.xml`.
* The debug topic `bar` yields output only of the last template in `sub.xml`.

> 📎
> Note that there is also a [`debug` _action_](debug.md). It can be used to create your own
> debug messages. Of course, it can also be filtered with debug topics.

```xml
<debug level="info" debug="foo" xpath="$foo"/>
```

This will log the content of the variable `$foo` if the topic `foo` has been selected.
