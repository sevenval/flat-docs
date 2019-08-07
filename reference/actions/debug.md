# `debug` Action

The `debug` action can be used to insert additional information into
the debug output of FIT if debugging is enabled.

A `debug` action has the following attributes:

* `channel="user|request|foo|bar..."` defines the channel used for the message. Defaults to `user`.
* `level="verbose|debug|info|warning|error|..."` defines the log level of the message. Defaults to `debug`.
* Either `in="..."` or `xpath="..."` defines the subject to be dumped to the message text.

Refer to the [debugging documentation](../debugging.md) for information
on how to enable and select debug output, and which levels are valid.

The `debug` action dumps the current internal representation of the subject
(usually XML in UTF-8 encoding). This may differ from both the content received
from the source as well as the final result sent to the client. The output of
the `debug` action will always be converted to UTF-8 encoding.

## Examples

```xml
<debug level="error" channel="bar" xpath="string('Error')" if="contains(request/url, '/error')"/>
<debug level="info" channel="dc" in="fit://request/dc" />
```

```xml
<!-- Sometimes the XML declaration line will be added to the output, e.g.: -->
<?xml version="1.0" encoding="iso-8859-1"?>

<!-- There is an output difference between the XML and HTML on empty elements: -->
<div class="myDiv"/>
<!-- instead of -->
<div class="myDiv"></div>
```

Note that there is a difference between the following two examples. If the
main URL `http://www.example.com/` is given:

```xml
<debug xpath="fit-document('fit://request/content/main/response')/response/@url" />
<!-- Output is: url="http://www.example.com/" -->

<debug xpath="string(fit-document('fit://request/content/main/response')/response/@url)" />
<!-- Output is: http://www.example.com/ -->
```

## Errors

The debug action will terminate the flow with an error message if not exactly
one of the attributes `xpath` and `in` is given, if the XPath is invalid or
cannot be evaluated, or if the `in` attribute does not provide a valid URL.
