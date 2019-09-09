# File Serving

Sometimes you want to serve local files, e.g. HTML, JavaScript, or CSS. This is a task for the [default flow](/reference/flow.md#default-flow) defined in `conf/flow.xml` and the [`serve` action](/reference/actions/serve.md).

> 📎
> In the following we assume that the `basePath` in `swagger.yaml` is *not* `/` so that
> the default flow will actually be effective.

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
  </head>
  <body>
    <h1>Fallback page</h1>
  </body>
</html>
```

and fetch it with `curl`:

```bash
curl -si http://localhost:8080/
HTTP/1.1 200 OK
Server: FLAT
Content-Type: text/html
…

<!DOCTYPE html>
<html>
  …
</html>
```

Let's add some style and script, too,

```bash
$ echo 'body { background-color: red }' > htdocs/styles.css
$ echo 'document.write("Hello!")' > htdocs/script.js
```

and reference them in `index.html`:

```html
    …
    <title>Fallback page</title>
    <link rel="stylesheet" href="/styles.css"> <!-- ⬅ -->
    <script src="/script.js"></script>         <!-- ⬅ -->
  </head>
  …
```

Now let's create another HTML document in a different directory:

```shell
mkdir somewhere
```

and save
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Somewhere</title>
    <link rel="stylesheet" href="/styles.css">
    <script src="/script.js"></script>
  </head>
  <body>
    <h1>Somewhere</h1>
    <p>This is somewhere.</p>
  </body>
</html>
```
to `somewhere/index.html`.

```shell
curl -si http://localhost:8080/somewhere
HTTP/1.1 301 Moved Permanently
Server: FLAT
Location: /somewhere/
Content-Type: text/html
…

```

Note the redirect.

```shell
curl -si http://localhost:8080/somewhere/
HTTP/1.1 200 OK
Server: FLAT
Content-Type: text/html
…

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Somewhere</title>
    <link rel="stylesheet" href="/styles.css">
    <script src="/script.js"></script>
  </head>
  <body>
    <h1>Somewhere</h1>
    <p>This is somewhere.</p>
  </body>
</html>
```

FLAT served the document in htdocs/somewhere/index.html, just like a webserver would do.

> 📎
> Note that requests to missing resources always return the fallback document.
