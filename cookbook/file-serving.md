# File Serving

Sometimes you want to serve local files, e.g. HTML, JavaScript, or CSS. This is a task for the [default flow](/reference/OpenAPI/routing.md#default-flow) defined in `conf/flow.xml` and the [`serve` action](/reference/actions/serve.md).

📎 **Note:**
In the following we assume that the `basePath` in `swagger.yaml` is *not* `/` so that
the default flow will actually be effective.

First, we create `flow.xml` in `conf` with the following content:

```xml
<flow>
  <serve dir="../htdocs" fallback-doc="/index.html"/>
</flow>
```

This assumes that the local files should be found in an `htdocs` directory next to `conf` (`dir="../htdocs"`) and that there is an `index.html` fallback document in the `htdocs` directory.

So, let's create `htdocs/index.html`

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Fallback page</title>
    <link rel="stylesheet" href="/styles.css">
    <script src="/script.js"></script>
  </head>
  <body>
    <h1>Fallback page</h1>
  </body>
</html>
```

and some assets:

```bash
$ echo 'body { background-color: red }' > htdocs/styles.css
$ echo 'document.write("Hello!")'       > htdocs/script.js
```

Finally, we create another HTML document in a different directory:

```bash
$ mkdir -p htdocs/some/where

$ echo '<html><body><h1>Somewhere</h1></body></html>' > htdocs/some/where/index.html
```

Let's go surfin' now:

```bash
$ curl -si localhost:8080
HTTP/1.1 200 OK
Server: FLAT
Content-Type: text/html
…

    <h1>Fallback page</h1>
  </body>
</html>

$ curl -si localhost:8080/styles.css
HTTP/1.1 200 OK
…
Content-Type: text/css

body { background-color: red }
```

Note the redirection when we request `/some/where` without the trailing `/`:

```bash
$ curl -si --location localhost:8080/some/where
HTTP/1.1 301 Moved Permanently
Server: FLAT
Location: /some/where/
Content-Type: text/html
…

HTTP/1.1 200 OK
…
<html><body><h1>Somewhere</h1></body></html>
```

FLAT responds with `index.html` just like a web server would do.

📎 **Note:**
Requests to missing resources always return the fallback document.
Therefore, any resources therein must be absolutely referenced.

```bash
$ curl -si localhost:8080/not/existing/
HTTP/1.1 200 OK
Server: FLAT
Content-Type: text/html
…

    <h1>Fallback page</h1>
  </body>
</html>
```
