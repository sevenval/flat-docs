# Debugging

Debugging is configured via the `$FLAT_DEBUG` environment variable. Its value is composed of at most three parts separated by colons (`:`):
`topic:level:sink`.

_topic_ denotes a topic you're interested in, for example a component. The default is `*` meaning "all topics". Several comma-separated topic identifiers are also valid.

_level_ specifies the log level. Valid values are `verbose`, `debug`, `info`, `warn`/`warning`, `error` and `alert`, with `debug` being the default. `warn` and `warning` are equivalent.

_sink_ identifies the log sink. Valid values are `inline`, `append` and `log`. The default value is `log`.
The values `inline` and `append` mostly make sense when debugging single requests via the `Debug` header, see below.

The empty string for all three parts is also valid with the same meaning as the default value `*:debug:log`.

If the third part is missing, the default value for sink (`log`) is assumed. If both the second and third part are missing, the default values for level and sink are assumed (`debug:log`).

## Examples

* `template` = `template:debug:log`
* `*:error` = `*:error:log`
* `flow,template` = `flow,template:debug:log`
* `::inline` = `*:debug:inline`
* `template::inline` = `template:debug:inline`
* `:alert:` = `*:alert:log`

## Per Request Debugging

The default debugging configuration set via `$FLAT_DEBUG` may be varied for single requests by
sending the desired parameters in the HTTP `Debug` header field, for example:

```bash
$ curl -H "Debug: request:info:append" localhost:8080
```

Per request debugging may be password-protected via the `$FLAT_DEBUG_AUTH` environment variable.
(In a non-Docker installation please use the `$FIT_DEBUG_AUTH_PASSWORD` environment variable instead).
If such authorization is required, the password has to be appended as `;auth=…` in the `Debug` header, for example:

```bash
$ curl -H "Debug: flow::append; auth=Pas5W0Rd" localhost:8080
```

## Selective Action Debugging

Using an action type, such as `template`, for debugging often yields a large
amount of information. Therefore, the debugging topic can be further limited by using the [`debug`
attribute](actions/README.md#debug-attribute) which is available on all actions.

```xml
<flow>
  <template>{"foo": 1}</template>
  <template debug="bar">{"bar": 2}</template>
</flow>
```

Given the flow above, debugging the `template` topic will log all template operations:

```bash
$ curl -H "Debug: template" localhost:8080
```

However, as we tagged the second `template` action with the debug topic `bar`
we are able to debug it separately:

```bash
$ curl -H "Debug: bar" localhost:8080
```

An action can have multiple comma-separated tags in the `debug` attribute.
Each tag can also be used in several actions, for example to combine related actions
into one common debug topic.

Usually you will make up your own tag names. But you can also use FLAT's system
topics such as `request` or other action types:

```xml
<flow>
  <template debug="request" out="$url">{{ concat("http://", "example.com") }}</template>
  <request>
  {
    "url": {{ $url }}
  }
  </request>
</flow>
```

When debugging that flow with topic `request`, you will get information about the request and also see where its URL came from.

---

See also:
* [Tutorial](../tutorial/README.md#when-things-go-south)
* [`debug` action](actions/debug.md)
