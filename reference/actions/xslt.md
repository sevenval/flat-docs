# `xslt` Action

The `xslt` action performs an [XSL 1.0 Transformation](http://www.w3.org/TR/xslt) on DOM content.

## Syntax

* `src="..."` path to the XSLT file (required)
* `cache="..."` controls whether the XSLT compiler cache shall be used (optional, default is `true`).
  Cache usage depends on the `$FLAT_XSLT_USE_CACHE`.
* `in="..."` to define the input location (optional, default is `fit://request/content`)
* `out="..."` to define the output location (optional, default is `fit://request/content`)


## Usage

The action reads the `in` content. If the input is no DOM, the action tries to
parse the content automatically. The XSLT code is read from the file defined in
`src`. The XSLT is then compiled (or read from the in-memory cache). The
transformation generates a new content (usually a DOM), that is written to `out`.

The `<xsl:output>` element is important for the action to work. The `method`
may be `html` and `xml` that generate a new DOM or `text` that generates an
output string. The `encoding` and `media-type` attributes are passed on to the
meta data of the FIDJ location. If you process the main content, it will be
passed to the client as `Content-type` header.

The XSLT cache stores a compiled representation of the XSLT stylesheet in the
process memory. This is usually recommended, because it avoids re-parsing and
re-compiling the same code over and over again. The action uses the
modification time of the `src` file to invalidate the cache.  This means that
the cache does not consider `xsl:include`, `xsl:import` or `x:include`
directives that you may have used to  spread your XSLT code over multiple files
to determine if a cache entry is still up to date.  You may clear the XSLT
cache by executing `fitadmin maintenance clearcache` for your project.
Alternatively, you can completely turn off caching if it hinders your
development process.  Then, if possible, re-enable caching when the site goes
in production.

### Examples

```xml
<xslt src="myXSLT.xsl" />
<xslt src="fit://site/config/myXSLT.xsl" cache="false" />
<xslt src="myXSLT.xsl" in="fit://request/content/test" out="fit://request/content/test" />
```

A simple HTML copy transformation looks like this:

```xslt
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">

<xsl:output method="html" />

<!-- your templates here -->
<xsl:template match="div[@class='special']" >
  <div class="very special">
    <div>this is a special tag</div>
    <xsl:apply-templates />
  </div>
</xsl:template>

<xsl:template match="node() | @*">
  <xsl:copy>
    <xsl:apply-templates select="@* | node()"/>
  </xsl:copy>
</xsl:template>

</xsl:stylesheet>
```

## Tips

If you rather want to generate a new document with your `xslt` action than
transforming an existing DOM, you can use `in="fit://request/empty"`.  This
special location always returns `<empty/>`.

You can clear the XSLT cache with the `fitadmin` command line tool using the
`--xslt` option:

    $ fitadmin maintenance clearcache --xslt myproject


## Errors

The following errors are considered fatal and terminate the request:

* XSLT not given or not found
* XSLT empty
* XSLT has syntax or compile errors

The following errors are considered non-fatal. The [flow](../flow.md)
will continue, no content will be written to `out`.

* The `in` document is not present, empty or no DOM
* The transformation raises runtime errors
