# Configuration

## Swagger/OpenAPI

Most settings for routing, validation and CORS can be set in `swagger.yaml`. See [OpenAPI Integrations](OpenAPI/README.md) for detailed information.


## Dynamic Configuration

If you need to define configuration settings dynamically, you can use the `conf/config.xml` file.

It supports the same settings as in `swagger.yaml`:

```xml
<config>
  <flat>
    <definition src="swagger.yaml"/>
    <validation request="true" response="report-only"/>
    <cors allowed-origins="http://localhost:9000" allow-credentials="true"/>
  </flat>
</config>
```

The difference is, that you may use _Dynamic Attribute Values_ and if-clauses as in the [flow](flow.md):

```xml
<config>
  <flat>
    <validation request="true" if="$server/role = 'dev'"/
    <validation request="report-only" if="$server/role = 'prod'"/
  </flat>
</config>
```
