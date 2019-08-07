# Sending POST Requests

## Posting Raw Data

We can send simple POST requests to upstream systems with the [`request` action](../reference/actions/request.md) as follows:

```xml
<flow>
  <request>{ "url": "https://httpbin.org/post", "method": "POST" }</request>
</flow>
```

The supplied JSON response from `https://httpbin.org/` reflects the request we've just sent:

```json
{
  "args": {},
  "data": "",
  "files": {},
  "form": {},
  "headers": {
    "Accept": "*/*",
    "Accept-Encoding": "deflate, gzip",
    "Content-Length": "0",
    "Content-Type": "application/x-www-form-urlencoded",
    "Host": "httpbin.org",
    "User-Agent": "Sevenval FIT"
  },
  "json": null,
  "origin": "88.130.59.205, 88.130.59.205",
  "url": "https://httpbin.org/post"
}
```

Let's now send some data using the
[`body` field](../reference/actions/request.md#body). Since the HTTP method
will be automatically set to `POST`, we don't need to specify it explicitly:

```xml
<flow>
  <request>
  {
    "url": "https://httpbin.org/post",
    "body": {
      "value": "answer=41+1&amp;foo"
    }
  }
  </request>
</flow>
```

The response shows that FLAT sends the data as `text/plain`:

```json
{
  …
  "data": "answer=41+1&foo",
  …
  "form": {},
  "headers": {
    …
    "Content-Length": "15",
    "Content-Type": "text/plain",
    …
  },
  …
}
```

> 📎 By the way, have you noticed how we XML-encoded the ampersand as
> `&amp;` in the request?

To send the data as URL-encoded form data `answer=41+1&foo"` we also have to
set the MIME type accordingly:

```json
    …
    "body": {
      "mime": "application/x-www-form-urlencoded",
      "value": "answer=41+1&amp;foo"
    }
    …
```

```json
{
  …
  "data": "",
  …
  "form": {
    "answer": "41 1",
    "foo": ""
  },
  "headers": {
    …
    "Content-Type": "application/x-www-form-urlencoded",
    …
  },
  …
}
```

Good! But still not what we wanted! The `+` got lost.
Not only do we have to consider the XML context we're in (`&amp;`),
but we also need to URL-encode the data ourselves, as the `body` field contains the _raw_ POST data as they will be sent upstream:

```json
      "value": "answer=41%2B1&amp;foo"
```

Or, with the help of the [`urlencode` function](../reference/functions/urlencode.md):

```json
      "value": {{ concat("answer=", urlencode("41+1"), "&amp;foo") }}
```

Yeah, that hurts pretty much!

The [`post` field](../reference/actions/request.md#post) to the rescue!

## Posting Form Data

We can achieve the same result with the following, much simpler request
using the [`post` field](../reference/actions/request.md#post).
The POST data is assembled automatically, URL-encoding included:

```xml
<flow>
  <request>
  {
    "url": "https://httpbin.org/post",
    "post": [
      {"name": "answer", "value": "41+1" },
      {"name": "foo",    "value": "" }
    ]
  }
  </request>
</flow>
```

Thus, for submitting URL-encoded form data, the `post` field is the way to go.

## Posting JSON

Sending JSON data is easy. We just supply the desired JSON as object for `value`:

```xml
<flow>
  <request>
  {
    "url": "https://httpbin.org/post",
    "body": {
      "value": { "answer": 42 }
    }
  }
  </request>
</flow>
```

The MIME type is set automatically:

```json
{
  …
  "data": "{\"answer\":42}",
  …
  "headers": {
    …
    "Content-Type": "application/json",
    …
  },
  "json": {
    "answer": 42
  },
  …
}
```

To send data from a JSON file we might use the [`json-doc` function](../reference/functions/json-doc.md):

```xml
    …
    "body": {
      "value": {{ json-doc("/files/answer.json") }}
    }
    …
```

See [below](#reading-post-data-from-files) for a general way to send content from a file.

## Posting XML

To send XML data we set the MIME type to `text/xml` and supply the XML payload in the `body` field:

```xml
<flow>
  <request>
  {
    "url": "https://httpbin.org/post",
    "body": {
      "mime": "text/xml",
      "value": "<![CDATA[<answer>42</answer>]]>"
    }
  </request>
</flow>
```

> 📎
> Instead of using `<![CDATA[…]]>` we could have written `&lt;answer>…`.

```json
{
  …
  "data": "<answer>42</answer>",
  …
  "headers": {
    …
    "Content-Length": "19",
    "Content-Type": "text/xml",
    …
  },
  …
}
```

## Reading POST Data from Files

Sending XML like this is again rather ugly. Fortunately, the body does not have to be placed inline. We can also make use of the `body`
field's `src` property to specify a location from where the body content should be read:

```xml
    …
    "body": {
      "mime": "text/xml",
      "src": "fit://site/files/answer.xml"
    }
    …
```

This way is binary safe, so we're able to send arbitrary content, an image for example:


```xml
<flow>
  <request>
  {
    "url": "https://httpbin.org/post",
    "body": {
      "src": "fit://site/files/image.jpg"
    }
  }
  </request>
</flow>
```

As before, the MIME type can be explicitly set with `mime` or, if missing, FLAT
tries to determine it automatically:

```json
{
  …
  "data": "data:application/octet-stream;base64,/9j/4AAQSkZJR…
  …
  "headers": {
    …
    "Content-Length": "1689",
    "Content-Type": "image/jpeg",
    …
  },
  …
}
```
