# Testing Templates

You have written a [JSON Template](/reference/Templating/README.md) and now you want to test it. FLAT comes with a [Test Framework](/reference/Testing/README.md) that makes it easy to exercise your code with various input and error conditions.

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

```shell
$ curl -s -d 'a=b&c=d' localhost:8080/api/jt?data=foo |jq
```

The result is:

```json
{
  "method": "POST",
  "path": "/api/jt",
  "numPostFields": 2,
  "data": "foo"
}
```
So far, so good. If you look closely, you will notice that the template is buggy. But we will discover later when writing tests for it :)

# Writing a Test

In FLAT, tests are written in `<flat-test>` XML files. Those tests work like regular [flows](/reference/flow.md), but they provide specialized [actions](/reference/actions/action.md#testing) to make testing easy.

Usually tests should run in the context of the FLAT app. Therefore, we recommend putting tests in a `tests/` folder next to the `swagger.yaml`:

```shell
$ ls my-project
swagger.yaml
api/
htdocs/
tests/
```

A test usually comprises
* the flow code under test
* setup code to provide input/env data
* assertions

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

The [`flat` cli](/reference/flat-cli.md) has a `test` command to run tests. It assumes to be called inside the FLAT app dir (where `swagger.yaml` is).

```shell
$ flat test tests/test-request-tpl.xml
1..1
ok 1 tests/test-request-tpl.xml: 1 assertions
passed: 1, failed: 0
```

Great! But not as great, yet…

## Refactor for Testing

In the test, we had to _copy_ the template from our production code. This doesn't really make sense. Because, now we test an isolated copy. If we break the actual flow, the test will still pass.

There are a couple of option to get about this.

We could extract the template body into a file:

```xml
<flow>
  <template src="request.tpl" />
</flow>
```

Now, in the test, we only repeat the action call:

```xml
<flat-test>
…
  <template src="../api/request.tpl" />
…
</flat-test>
```

Note, that we provide a different `src` attribute, because the relative path to the template differs for the the flow and the test file.

Another approach is to extract the template code into a [sub flow](/reference/actions/sub-flow.md). This can be called by the path flow and the test:

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

Now we can put the expected JSON string into the _golden file_ `tests/request-info.golden:

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

The `json` mode ensures that both, wanted and actual results, are valid JSON. The comparison is independent of the JSON formatting. This allows us the have the golden file pretty printed:

```json
{
  "method": "POST",
  "path": "/a/path",
  "numPostFields": 3,
  "data": "foo"
}
```

This makes changes to the file easier during development. Especially, your co-workers will thank you for a readable git diff.

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
```shell
$ flat test tests/test-request-tpl.xml
1..1
ok 1 tests/test-request-tpl.xml: 1 assertions
passed: 1, failed: 0
```