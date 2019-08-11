# Extracting Common Initialization Flow Tasks

You don't t like to repeat yourself. And that holds true for your API flows,
too! The [_init flow_](/reference/flow.md#init-flow) helps you avoiding that.
You can extract common initialization tasks that every flow would need into an
_init flow_ that is executed before the regular flow for your API paths.

This is a good place to
* set common response headers,
* initialize global variables,
* force error handling by setting status `500`,
* gather configuration data,
* or validate JWT tokens.

In your `swagger.yaml`, point the top-level property `x-flat-init` to a flow
file:

```yaml
…
x-flat-init: init.xml       # ⬅ init flow
paths:
  x-flat-flow: default.xml  # ⬅ fallback flow
  /:
    get:
      x-flat-flow: get.xml  # ⬅ regular path flow
…
```

In this example, we use `init.xml` to set some global configuration, and
provide the request's unique id to downstream systems in the `Request-ID`
header (for log correlation).

```xml
<flow>
  <template out="$cfg">
    {
      "stage": {{ $server/role ?? "prod" }},
      "upstream_api": ${{ $env/API_ORIGIN ?? "localhost:3000" }}
    }
  </template>
  <set-response-headers>
    {
	  "Request-ID": {{ $request/id }}
	}
  </set-response-headers>
</flow>
```

Now, we can rely on these preparations in all API flows. You can use the `$cfg`
variable to build your upstream request URL, or to check the current stage.
Every response for a valid endpoint will have a `Request-ID` header.
