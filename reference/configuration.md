# Configuration

Example for `conf/config.xml`:

```xml
<config>
  <flat>
    <definition src="swagger.yaml"/>
    <validation request="true" response="report-only"/>
    <cors allowed-origins="http://localhost:9000" allow-credentials="true"/>
  </flat>
</config>
```

See also
* [Tutorial](../tutorial/README.md#openapi).
