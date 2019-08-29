# `set-env` Action

Used in [FLAT tests](/reference/testing/README.md) to change the environment of the FLAT app under test.

Normally, env vars are set on server start-up and are immutable later on. They are available in the pre-defined variable [`$env`](/references/variables.md#predefined-variables).

The action allows to override variables in the `$env` structure for testing purposes.

## Syntax

```xml
<set-env>
{
  "VAR_NAME": "VALUE",
  "BUILD_TIME": {{ unixtime() - 3600 }}
}
</set-env>
```

The action argument is a JSON Object. The property values should be of type string.

As env vars from the system (shell or `docker`) can only be strings, numeric or boolean values are cast to their _string representation_.

If the value is `null`, the env var is unset.

The action argument may be a [JSON template](/reference/templating/README.md).


## See also

* [`$env`](/reference/variables.md#predefined-variables) (reference)
* [Using Env Vars](/cookbook/envvars.md) (cookbook)
* [Testing](/reference/testing/README.md) (reference)
