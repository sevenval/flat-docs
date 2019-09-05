# `fit-log()`

```
boolean fit-log(string message)
```

The XPath function `fit-log` writes the given `message` into the
`fit_engine.log`. Unless invalid parameters are supplied, the `fit-log`
function always returns `true()`. Therefore, it can easily be used
in the `if` attribute of all flow actions, as shown in the example below.

* `message`: a message to be written into the `fit_engine.log`.

## Example

```markup
<flow>
  <xslt if="fit-log('Process my XSLT')" src="XSLT.xsl" />
</flow>
```

Example `fit_engine.log` log line:

```
2015-12-01 11:12:13+0200 VmaNmn8AAAEAAB3Gy08AAABA myProject Process my XSLT
```
