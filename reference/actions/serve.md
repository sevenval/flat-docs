# `serve` Action

The `serve` action can be used to deliver local files to clients. The action implements a simple file server. Optionally, processing of the flow can continue if the content provided by the `sever` action needs to be modified.

## Syntax

`dir` and `fallback-doc` are mandatory:

* `dir="..."` defines the directory from which the local files are read. The directory must exist and must be within the project path. The `dir` directory is resolved relative to the calling flow file.
* `fallback-doc`: Defines a fallback document. This document will be used if the requested file cannot be found. The `fallback-doc` file is resolved relative to the calling flow file.
* `terminate="true|false"`: if `true`, continue processing the flow, otherwise the file loaded by `server` is sent to the client and the flow is aborted.

* the action has no input (`in`)
* if further processing of the loaded file is required, the output of `server` can be redirected via the `out` attribute

```xml
<flow>
  …
  <serve dir="../htdocs" fallback-doc="/index.html"/>
  …
</flow>
```

## Usage

The `request/path` is read from the [Delivery Context](../ress/DC_Props.html) and resolved relative to the `dir`. If the resolved path references a directory instead of a file, `index.html` within that directory is used instead. If the `request/path` references a directory but does not end with a slash (`/`), a `301 Moved Permanently` redirect to the same path with a terminating slash (`/`) is executed.

If a file is found, the following HTTP header fields are set:

* `Content-Type`
* `Content-Length`
* `Last-Modified`

The following [Delivery Context properties](../ress/DC_Props.html) are set:

* `content/<type>` (so called handling type, e.B. `content/html` for `text/html`)
* `content/mime` (identical to the `Content-Type` HTTP header field, e.g. `text/html`)

`Content-Type` and the Delivery Context properties are determined by the suffix of the file. The HTTP status code is set to `200 OK`.

If the file is not readable, the HTTP status is set to `403 Forbidden`.

## Errors

* The attribute `dir` is missing.
* The attribute `fallback-doc` is missing.
* The directory referenced by `dir` does not exist or cannot be read.
* The directory referenced by `dir` is not within the project directory.
* The file referenced by `fallback-doc` is to be loaded but does not exist or cannot be read.

## See also

* [File Serving](/cookbook/file-serving.md) (cookbook)
* [Default Flow](/reference/flow.md#default-flow)
