# `content()`

The `content` function is a convenient function to access
upstream response bodies from inside the [flow](../flow.md).

```xml
<flow>
  <if test="content()/html/head/title = 'Thank you'">
    …
  </if>
</flow>
```

When called without parameters, the function returns the main content DOM from
`fit://request/content`.

The following `test` expressions are equivalent:

* `content()/html/head/title`
* `content('')/html/head/title`
* `fit-document('fit://request/content')/html/head/title`
* in XSLT you may use the standard function `document('fit://request/content')/html/head/title`

Be aware that `fit://request/content` and `fit://request/content/main` are not
the same thing! While the first starts as a copy of the latter, flow actions may
alter `fit://request/content` throughout the request.
`fit://request/content/main` holds the plain text response body and usually
remains unchanged. Thus, `content()` and `content('main')` are not necessarily
equivalent.

Optionally, a [content ID](../actions/requests.md) can
be passed to read data from `fit://request/content/<ID>`:

* `content('side')/some/data`

The `content` function returns a
[node-set](http://www.w3.org/TR/xpath/#node-sets) that contains the document
node of the DOM in `fit://request/content/<ID>`. When the content does not
exist, the node-set will be empty.
