# How can I see what the client requested?

Information about the client request is available in `$request`. To see what's inside, you can use the
following flow which copies its content into the response using a
[pair producer](../reference/templating/pair-producer.md):

```xml
<flow>
  <template in="$request">{ {{: *}} }</template>
</flow>
```
