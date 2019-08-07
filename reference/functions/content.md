# `content()`

The `content` XPath function is a convenient function to access
contents from inside the [flow](../flow/Flow.html). However, as an
XPath function you may use it in expressions outside the flow, too.

```markup
<flow>
  <default-request />
  <if test="content()/html/head/title = 'Thank you'">
    ...
  </if>
  ...
</flow>
```

When called without parameter, the function returns the main content DOM from
`fit://request/content`.

The following XPath expressions are equivalent:

* `content()/html/head/title`
* `content('')/html/head/title`
* `fit-document('fit://request/content')/html/head/title`
* in XSLT you may use the standard function `document('fit://request/content')/html/head/title`

;;block-note

Be aware that `fit://request/content` and `fit://request/content/main` are not
the same thing! While the first starts as a copy of the latter, flow actions may
alter `fit://request/content` throughout the request.
`fit://request/content/main` holds the plain text response body and usually
remains unchanged. Thus, `content()` and `content('main')` are not necessarily
equivalent.

block;;

Optionally, a [content ID](../flow/FlowAction_Requests.html) can
be passed to read data from `fit://request/content/<ID>`:

* `content('side')/some/data`

The `content` function returns a
[node-set](http://www.w3.org/TR/xpath/#node-sets) that contains the document
node of the DOM in `fit://request/content/<ID>`. When the content does not
exist, the node-set will be empty.
