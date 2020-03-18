# Processing Upstream Responses

Let's take a look at how responses from upstream systems provided by
[`request`](/reference/actions/request.md)/[`requests`](/reference/actions/requests.md)
or [`proxy-request`](/reference/actions/proxy-request.md) actions can be processed
further.

The [`body` function](/reference/functions/body.md) provides you with the raw body of
an HTTP response. For further processing, it may be necessary to parse the body using
functions like [`json-parse`](/reference/functions/json-parse.md) or
[`xml-parse`](/reference/functions/xml-parse.md):

```xml
<flow>
  <requests>{
    "xml":  { "url": "…" } },
    "json": { "url": "…" } }
  }
  </requests>

  <eval out="$upstream-json">json-parse(body('json'))</eval>
  <eval out="$upstream-xml">xml-parse(body('xml'))</eval>

  <if test="$upstream-json/success">…</if>
  …
</flow>
```

The [`$upstream` variable](/reference/variables.md) gives additional information about
upstream responses like the HTTP status and the headers. The following
recursive [sub-flow](/reference/actions/sub-flow.md) demonstrates a general way how to
follow a redirect:

request.xml:
```xml
<flow>
  <request>{"id": "my-request", "url": {{ $url }}}</request>

  <if test="$upstream/my-request/status = 302">
    <eval out="$url">$upstream/my-request/headers/location</eval>
    <sub-flow src="request.xml"/>
  </if>
</flow>
```

To just pass the upstream response downstream you can use the
[`pass-body` action](/reference/actions/pass-body.md):

```xml
<flow>
  <request>{"url": "http://example.com"}</request>
  <pass-body/>
</flow>
```
