# Initializing every Flow

If you want to initialize every flow you have defined, e.g. because you want
to set a common set of response headers, or you need a common set of variables
in every flow, you can use `x-flat-init` in your `swagger.yaml` to specify an
init flow.

swagger.yaml:
```yaml
…
x-flat-init: init.xml       # ⬅ init flow
paths:
  x-flat-flow: default.xml  # ⬅ fallback flow
  /:
    get:
      x-flat-flow: get.xml  # ⬅ flow for GET /
…
```

init.xml:
```xml
<flow>
  <template out="$init">
    {
      "needed": "in every flow"
    }
  </template>
  <set-response-headers>
    {
	  "Needed": "in every response"
	}
  </set-response-headers>
</flow>
```

default.xml:
```xml
<flow>
  <template>
    {
…
      "foo": {{ $init/needed }}
…
    }
  </template>
</flow>
```

get.xml:
```xml
<flow>
  <template>
    {
…
      "bar": {{ $init/needed }}
…
    }
  </template>
</flow>
```
