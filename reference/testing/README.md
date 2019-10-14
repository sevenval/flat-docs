# Testing

FLAT provides a built-in test framework. It is especially useful to unit-test [templates](/reference/templating/README.md), error handling and upstream requests.

## Syntax

FLAT tests are defined as [flow](/reference/flow.md) files with the root node `flat-test`. There are a couple of specialized [test actions](/reference/actions/README.md#test-actions) that accompany the test flow. The invocation of at least one [`assert` action](/reference/actions/assert.md) is mandatory.

```xml
<flat-test>
  <!-- flow code -->

  <assert>
  [
    [ … ]
  ]
  </assert>
</flat-test>
```

## Directories

Test files can be stored anywhere inside the FLAT app directory. We recommend creating a `tests/` folder next to `swagger.yaml`.

## Command

FLAT tests are started with the [`flat` cli](/reference/flat-cli.md) command `test`:

```bash
$ flat test tests/test-foo.xml tests/test-bar.xml
```

The arguments can either be files or directories. For each directory, `flat test` will recursively collect all files in that directory and its subdirectories that contain XML text and have the root element `flat-test` and run them. `flat test` will execute all non-directory arguments as tests and flag errors if they are not readable regular files with XML root element `flat-test`.

The test result is printed in [TAP](https://testanything.org) format:

```tap
1..2
ok 1 tests/test-foo.xml: 17 assertions
ok 2 tests/test-bar.xml: 4 assertions
passed: 2, failed: 0
```

If _all_ tests pass, the exit code is `0`. If _any_ test fails, the code is non-zero (`1`). Therefore, in deployment scripts or `Makefile` recipes (both of which are usually executed as `set -e`) you can rely on the exit code to fail the process.

## See also

* [Testing Templates](/cookbook/test-templates.md) (cookbook)
* [Testing API Requests](/cookbook/test-api-request.md) (cookbook)
* [Testing Upstream Requests](/cookbook/test-backend.md) (cookbook)
* [Test Actions](/reference/actions/README.md#test-actions) (reference)
  * [`assert`](/reference/actions/assert.md) (reference)
  * [`backend-flow`](/reference/actions/backend-flow.md) (reference)
  * [`set-env`](/reference/actions/set-env.md) (reference)
  * [`test-request`](/reference/actions/test-request.md) (reference)
