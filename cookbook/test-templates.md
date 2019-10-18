# Testing Templates

You have written a [JSON Template](/reference/templating/README.md) and now you want to test it. FLAT comes with a [Test Framework](/reference/testing/README.md) that makes it easy to execute your code with various input and error conditions.

## Our Test Specimen
Let's write a simple template that outputs some information on the incoming request. (You have to register it in your [`swagger.yaml`](/reference/OpenAPI/routing.md), we assume it is stored as `api/request-info.xml`).

```xml
<flow>
  <template>
  {
    "method": {{ $request/method }},
    "path": {{ $request/path }},
    "numPostFields": {{ count($request/post/*) }},
    {{if $request/get/data }}
      "data": {{ $request/get/data }}
    {{end}}
  }
  </template>
</flow>
```

Looks easy. Let's try it out:

```bash
$ curl -s -d 'a=b&c=d' localhost:8080/api/path?data=foo |jq
```

The result is:

```json
{
  "method": "POST",
  "path": "/api/path",
  "numPostFields": 2,
  "data": "foo"
}
```
So far, so good. If you look closely, you will notice that the template is buggy. But we will bump into this when writing tests for it :)

## Writing a Test

In FLAT, tests are written in `<flat-test>` XML files. Those tests work like regular [flows](/reference/flow.md), but they provide specialized [actions](/reference/actions/README.md#test-actions) to make testing easy.

Usually, tests should run in the context of the FLAT app. Therefore, we recommend putting tests in a `tests/` folder next to the `swagger.yaml`:

```bash
$ ls my-project
swagger.yaml
api/
htdocs/
tests/
```

A test usually comprises
* the flow code under test
* setup code to provide input/env data
* [assertions](/reference/actions/assert.md)

Create `tests/test-request-tpl.xml`:

```xml
<flat-test>
  <!-- setup input data -->
  <template out="$request">
  {
    "method": "POST",
    "path": "/a/path",
    "post": {
      "a": 1,
      "b": 2,
      "c": 3
    },
    "get": {
      "data": "foo"
    }
  }
  </template>

  <!-- code under test -->
  <template out="$result">
  {
    "method": {{ $request/method }},
    "path": {{ $request/path }},
    "numPostFields": {{ count($request/post/*) }},
    {{if $request/get/data }}
      "data": {{ $request/get/data }}
    {{end}}
  }
  </template>

  <!-- assertions -->
  <assert>
  [
    [ "json-stringify($result)", "{\"method\":\"POST\",\"path\":\"/a/path\",\"numPostFields\":3,\"data\":\"foo\"}" ]
  ]
  </assert>
</flat-test>
```

The [`flat` cli](/reference/flat-cli.md) has a `test` command to run tests. It it easiest to run it from  inside the FLAT app dir (where `swagger.yaml` resides).

```bash
$ flat test tests/test-request-tpl.xml
1..1
ok 1 tests/test-request-tpl.xml: 1 assertions
passed: 1, failed: 0
```

Great! But there's still room for improvement…

## Refactor for Testing

In the test, we had to _copy_ the template from our production code. This doesn't really make sense. Because, now we test an isolated copy. If we break the actual flow, the test will still pass.

There are a couple of options to go about this.

We could extract the template body into a file:

```xml
<flow>
  <template src="request.tpl" />
</flow>
```

Now, in the test, we only repeat that action call:

```xml
<flat-test>
  …
  <template src="../api/request.tpl" />
  …
</flat-test>
```

Note, that we provide a different `src` attribute, because the relative path to the template differs for the flow and the test file.

Another approach is to extract the template code into a [sub flow](/reference/actions/sub-flow.md). This can be called both by the path flow and the test:

```xml
<flat-test>
  …
  <sub-flow src="../api/request-info.xml" />
  …
</flat-test>
```

This looks much better! We now test the _real_ template call.

However, that call did not specify an `out` variable. To catch that template output in the test, we use the [`content()`](/reference/functions/content.md) function:

```xml
<assert>
[
  [ "json-stringify(content())", "{\"method\":\"POST\",\"path\":\"/a/path\",\"numPostFields\":3,\"data\":\"foo\"}" ]
]
</assert>
```

## JSON File Comparisons

But we can also make the assertion part of tests look better. For the beginning we were explicitly using `json-stringify()` to turn `$result` into a string. And even worse, that string had to be compared to a clunky, escaped JSON string.

The comparison parameter (2nd field of assertions) can also be an object with special flags. We can instruct `assert` to read the comparison value from a file:

```xml
<assert>
[
  [ "json-stringify($response)", {"file": "request-info.golden"} ]
]
</assert>
```

Now we can put the expected JSON string into the _golden file_ `tests/request-info.golden`:

```json
{"method":"POST","path":"/a/path","numPostFields":3,"data":"foo"}
```

We can still simplify that by using the `json` comparison mode:

```xml
<assert>
[
  [ "$response", {"file": "request-info.golden", "mode": "json"} ]
]
</assert>
```

The `json` mode ensures that both wanted and actual results are valid JSON. The comparison is independent of the JSON formatting. This allows us the have the golden file pretty printed:

```json
{
  "method": "POST",
  "path": "/a/path",
  "numPostFields": 3,
  "data": "foo"
}
```

This makes changes to the file easier during development. Especially your co-workers will thank you for a readable git diff.

## Full Example

`api/request-info.xml`:
```xml
<flow>
  <template>
  {
    "method": {{ $request/method }},
    "path": {{ $request/path }},
    "numPostFields": {{ count($request/post/*) }},
    {{if $request/get/data }}
      "data": {{ $request/get/data }}
    {{end}}
  }
  </template>
</flow>
```

`tests/test-request-tpl.xml`:
```xml
<flat-test>
  <!-- setup input data -->
  <template out="$request">
  {
    "method": "POST",
    "path": "/a/path",
    "post": {
      "a": 1,
      "b": 2,
      "c": 3
    },
    "get": {
      "data": "foo"
    }
  }
  </template>

  <!-- code under test -->
  <sub-flow src="../request-info.xml" />

  <assert>
  [
    [ "content()", {"file": "request-info.golden", "mode": "json"} ]
  ]
  </assert>
</flat-test>
```

`tests/request-info.golden`:
```json
{
  "method": "POST",
  "path": "/a/path",
  "numPostFields": 3,
  "data": "foo"
}
```

Call test:
```bash
$ flat test tests/test-request-tpl.xml
1..1
ok 1 tests/test-request-tpl.xml: 1 assertions
passed: 1, failed: 0
```

## Adding More Tests

So far, we have only tested one specific input. Let's provide another test with different input data, such as a `GET` request or one without the `data` query parameter. This will reveal a serious bug in our code!

You can copy the test to another file for the `GET` case. If you think those variants are closely related, you can also add more test code after the `<assert>` in our `tests/test-request-tpl.xml` like this:

```xml
<flat-test>

  <!-- test case from above -->
  …
  </assert>

  <!-- test GET -->
  <template out="$request">
  {
    "method": "GET",
    "path": "/"
  }
  </template>

  <sub-flow src="../request-info.xml" />

  <assert>[[ "content()", {"file": "request-info2.golden", "mode": "json"} ]]</assert>
</flow>
```

`tests/request-info2.golden` could look like this:

```json
{
  "method": "GET",
  "path": "/",
  "numPostFields": 0
}
```

What happens, when you call the `flat test` command now?

We get an execution error! Luckily the debug (`-d [topic]`) is enabled, so we see:

```
Action "template": Output is not valid JSON: Syntax error
```

📝 **Exercise:** Fix that bug in the template!

💡 Hint: The number one reason for invalid JSON syntax are [commas](/reference/templating/comma.md).

After we have fixed the bug in the template, the test result should look like this:

```bash
$ flat test tests/test-request-tpl.xml
1..1
ok 1 tests/test-request-tpl.xml: 2 assertions
passed: 1, failed: 0
```

💡 Hint: You can call `flat test` with multiple files:

```bash
$ flat test tests/test-*.xml
1..2
ok 1 tests/test-request-tpl.xml: 1 assertions
ok 2 tests/test-request-tpl-get.xml: 1 assertions
passed: 2, failed: 0
```

## Using Other Compare Flags

You can use more compare flags for your tests.

The `contains` flag can be used to test whether an expression result contains a given string:

```xml
<flat-test>
  <template out="$request">
  {
    "method": "POST",
    "path": "/a/path#"
  }
  </template>

  <assert>
  [
    [ "$request/path", {"contains": "a/"} ]
  ]
  </assert>
</flat-test>
```

This assertion tests whether the `path` property of `$request` contains the string "a/".

The `pattern` flag can be used to test whether an expression result matches a given regular expression:

```xml
  <assert>
  [
    [ "$request/method", {"pattern": "#^post$#i"} ]
  ]
  </assert>
```

This assertion tests whether the `method` property of `$request` matches case-insensitively the string "post".

## See also

* [Testing API Requests](test-api-request.md) (cookbook)
* [Testing Upstream Requests](test-backend.md) (cookbook)
* [Testing](/reference/testing/README.md) (reference)
* [`assert`](/reference/actions/assert.md) (reference)
