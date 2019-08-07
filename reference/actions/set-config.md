# `set-config` Action

The `set-config` action is used to override settings of the current
[configuration](../configuration.md) at runtime.

## Syntax

The action expects configuration elements as child nodes. It has no attributes.

Example:

```xml
<flow>
  <!-- Secure checkout -->
  <set-config if="contains($request/url, '/checkout')">
    <security>
      <force-backend-https enable="true"/>
    </security>
    <url-rewriting>
      <force-https/>
    </url-rewriting>
  </set-config>
  …
</flow>
```

## Behavior

The given configuration elements are merged with the existing settings in
[`conf/config.xml`](../configuration.md). If a config element is already set,
their attributes are merged. Otherwise, the element is copied into the
configuration.

If the `enable` attribute is not explicitly defined for a setting, `enable="true"` is assumed.
Therefore, the following examples are equivalent:

```xml
<force-https/>
<force-https enable="true"/>
```

Although the [config syntax](../configuration.md) forbids the redeclaration of
settings, you may override the same setting with `set-config` actions multiple
times.

> 📎
Be aware that the config settings are evaluated at different points of time.
Some settings, such as `security/force-client-https` are evaluated before the flow
starts. Others, such as `force-backend-https` affect requests that may run at any
time in the flow. Changing those settings with `set-config` _after_ they are
used, will have no effect. However, most options are used after the flow.
