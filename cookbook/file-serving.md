# File Serving

Sometimes you want to serve local files, e.g. HTML, JavaScript, or CSS. This is a task for the default flow (`conf/flow.xml`) and the [`serve` action](/reference/actions/serve.md).

First, create `flow.xml` in `conf` with the following content:

```xml
<flow>
  <serve dir="../htdocs" fallback-doc="/index.html"/>
</flow>
```

This assumes that the local files should be found in an `htdocs` directory next to `conf` (`dir="../htdocs"`) and that there is an `index.html` fallback document in the `htdocs` directory.

Now, let's create them

```shell
cd ..
mkdir htdocs
cd htdocs
```

and save

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Main page</title>
  </head>
  <body>
    <h1>Main page</h1>
    <p>This is the main page.</p>
  </body>
</html>
```
to `index.html`.

```shell
curl -si http://localhost:8080/
HTTP/1.1 200 OK
Server: FLAT
Content-Type: text/html
…

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Main page</title>
  </head>
  <body>
    <h1>Main page</h1>
    <p>This is the main page.</p>
  </body>
</html>
```

Now let's add some style and script:

```shell
mkdir css
mkdir js
```

and save

```css
body {
  color: #000;
  background: #fff
}

h1 {
  font-size: 2em
}
```
to `css/main.css`

and

```js
console.log('from main.js')
```
to `js/main.js`.

Now edit `index.html`:

```html
…
  <head>
    <meta charset="utf-8">
    <title>Main page</title>
    <link rel="stylesheet" href="/css/main.css"> <!---  ⬅ -->
    <script src="/js/main.js"></script>          <!---  ⬅ -->
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
    <link rel="stylesheet" href="/css/main.css">
    <script src="/js/main.js"></script>
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
    <link rel="stylesheet" href="/css/main.css">
    <script src="/js/main.js"></script>
  </head>
  <body>
    <h1>Somewhere</h1>
    <p>This is somewhere.</p>
  </body>
</html>
```

FLAT served the document in htdocs/somewhere/index.html, just like a webserver would do.
