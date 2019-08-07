# `dump` Action

The `dump` action terminates the request processing and dumps out
(intermediate) data to the client.

## Syntax

* `in="..."` defines the input location (optional, default is `fit://request/content`)
* `minify="true|false"` controls JS/CSS minification (optional, default is `true`)
* `mime="..."` sets the HTTP header field `Content-Type` to the given value (optional, no default)
* `status="..."` sets the HTTP status code to the given value (optional). If no `status` attribute is set, the HTTP status will remain unchanged. If `status` has an empty value (e.g. `status=""`), the HTTP status code is set to `500`.

## Usage

Before sending out the data specified in `in`, the `dump` action makes the following modifications:

* The meta data of `in` is used to determine the `Content-Type` header
* New cookies received from upstream servers are sent

The following modifications are applied to the content:

* remaining `fit:` URIs are stripped or translated
* JavaScript and CSS content is minified, if not disabled

## Examples

```xml
<dump />
<dump in="fit://site/public/error.html"/>
<dump in="fit://request/content" status="201" mime="text/plain"/>
```

The action is handy in debugging situations to terminate the [flow](../flow.md`)
at a certain position. In this example we dump the main content after
applying regular expressions but before parsing the content:

```xml
<flow>
  <request>…</request>
  <regex ... />
  <!-- debug: terminate after regex has been applied -->
  <dump/>

  …
</flow>
```
