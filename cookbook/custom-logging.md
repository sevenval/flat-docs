# Logging Custom Fields

FLAT writes [JSON logs](/administration/logging.md) for events like client access or upstream requests. The structured log events is easily extensible.

The [access log](/administration/logging.md#access-log) already provides a basic set of information about the incoming request. However, when inspecting (any) logs quickly the wish for more contextual information will arise.

Here are some ideas for fields to augment your logs with:
* A random "Tab" ID sent by the client to correlate API requests to a single SPA instance
* The version or git revision of your FLAT project
* A message to distinguish reasons for errors
* The staging role (dev/test/prod) or data center identifier passed in by [environment variables](envvars.md)
* The client's real IP address as advertised by a downstream system (e.g. your load balancer)
* User and Role from a [JWT token](jwt.md)
* …

We want all of that!

To play with logging, you need a FLAT project. If you don't have on, yet, have a look into the [tutorial](/tutorial/README.md) to [get started](/tutorial/README.md#getting-started).

## Reading logs

Before start customizing our logs, we need to setup our workplace in order to see what we are doing. Where can we inspect FLAT logs?

FLAT runs in [Docker](https://www.docker.com) and writes its log lines to `stdout` and `stderr`. (If you don't know the two, don't fear.) In a development setup, we usually start FLAT with the [`flat` cli](/reference/flat-cli.md). If you run
```sh
$ flat start
…
```
you will see the log output in your terminal. Some startup messages are plain text lines. The [debug output](/reference/debugging.md) will be text, too.

However, once you call your API with `curl` you will see JSON lines in your terminal:

```sh
$ curl localhost:8080/api/…
```
```json
{"timestamp":"2019-10-15T15:49:13+00:00","type":"flat_access","requestID":"XaXqeccky00IowY@OUgG8AAAAAA","path":"/api/…","status":200,"method":"GET","agent":"curl/7.54.0","referrer":"","mime":"application/json"}
```

You can have the JSON colored and pretty printed by piping the output to the [`jq` command](https://stedolan.github.io/jq/):

```sh
$ flat start | jq -R -r '. as $line | try fromjson catch $line'
```

Now, the access log will be nicely readable on your terminal:

```json
…
{
  "timestamp": "2019-10-15T15:49:13+00:00",
  "type": "flat_access",
  "requestID": "XaXqeccky00IowY@OUgG8AAAAAA",
  "path": "/api/…",
  "status": 200,
  "method": "GET",
  "agent": "curl/7.54.0",
  "referrer": "",
  "mime": "application/json"
}
```


## Adding a log field

Now that we know where our logs go, we're finally ready to add fields!

We will start with a simple task: Let's add a field to our log that contains a fixed string.

This can actually be useful: if you share a log server with other FLAT projects, the field let's you identify the originating project. (In production, your log shipper will probably provide this information already in its log envelope.)

Log fields can be registered with the [`log` action](/reference/actions/log.md). The action takes a [JSON template](/reference/templating/README.md) as its argument. For our fixed field, we write:

```xml
<log>
{
  "project": "myAPIProject"
}
</log>
```

A good place for this action is the [init flow](/reference/OpenAPI/routing.md#init-flow) because it is started for all API flows.

Call your FLAT API again and watch the logs:

```sh
$ curl localhost:8080/api/…
```
```json
{
  "timestamp": "2019-10-15T15:49:13+00:00",
  "type": "flat_access",
  "requestID": "XaXqeccky00IowY@OUgG8AAAAAA",
  "path": "/api/…",
  "status": 200,
  "method": "GET",
  "agent": "curl/7.54.0",
  "referrer": "",
  "mime": "application/json",
  "project": "myAPIProject"
}
```

Our first custom log field has hit the terminal!

## Adding dynamic log fields

With [JSON templates](/reference/templating/README.md) you can use expressions to dynamically set field names and values.

Let's assume, you use the [environment variable](envvars.md) `FLAT_STAGE` to tell your project in which deployment stage it is running. Possible values could be `dev`, `qa` and `prod`. Another variable `FLAT_DC` holds the airport code of the data center, e.g. `CGN` or `LON`.

To include this information into the log, we add more fields to our `log` action:

```xml
<log>
{
  "project": "myAPIProject",
  "stage": {{ $env/FLAT_STAGE ?? "unknown" }},
  "location": {{ $env/FLAT_DC }}
}
</log>
```

Notice, how we can provide default values for missing data. If _both_ of the variables are not set, a log may look like this:

```json
{
  "timestamp": "2019-10-15T15:49:13+00:00",
  "type": "flat_access",
  "requestID": "XaXqeccky00IowY@OUgG8AAAAAA",
  "path": "/api/…",
  "status": 200,
  "method": "GET",
  "agent": "curl/7.54.0",
  "referrer": "",
  "mime": "application/json",
  "project": "myAPIProject",
  "stage": "unknown"
}
```

`stage` is always defined. But `location` is missing, because its expression has evaluated to `null`. Nulled fields don't show up in the log.

## Structured log fields

Your custom log fields are not restricted to the top-level field-list. If you have more fields, that belong together, you can group them in an object.

In our case, we might want to gather information about the environment:

```xml
<log>
{
  "project": "myAPIProject",
  "env": {
    "stage": {{ $env/FLAT_STAGE ?? "unknown" }},
    "location": {{ $env/FLAT_DC }}
  }
}
</log>
```

Notice, how we can provide default values for missing data. If _both_ of the variables are not set, a log may look like this:

```json
{
  "timestamp": "2019-10-15T15:49:13+00:00",
  "type": "flat_access",
  "requestID": "XaXqeccky00IowY@OUgG8AAAAAA",
  "path": "/api/…",
  "status": 200,
  "method": "GET",
  "agent": "curl/7.54.0",
  "referrer": "",
  "mime": "application/json",
  "project": "myAPIProject",
  "env": {
    "stage": "production",
    "location": "CGN"
  }
}
```

## Using request data

Client's provide a lot of useful information in the HTTP request headers. We can easily use them to augment our log:


```xml
<log>
{
  "project": "myAPIProject",
  "env": {
    "stage": {{ $env/FLAT_STAGE ?? "unknown" }},
    "location": {{ $env/FLAT_DC }}
  },
  "request": {
    {{: $request/headers/x-real-ip | $request/headers/x-forwarded-proto }}
  }
}
</log>
```

Here, we use the [pair producer `{{:`](/reference/templating/pair-producer.md) to create log fields from headers. If present, the headers will show up in the log:

```sh
$ curl -H "X-Forwarded-Proto: https" localhost:8080/api/…
```
```json
{
  "timestamp": "2019-10-15T15:49:13+00:00",
  "type": "flat_access",
  "requestID": "XaXqeccky00IowY@OUgG8AAAAAA",
  "path": "/api/…",
  "status": 200,
  "method": "GET",
  "agent": "curl/7.54.0",
  "referrer": "",
  "mime": "application/json",
  "project": "myAPIProject",
  "env": {
    "stage": "production",
    "location": "CGN"
  },
  "request": {
    "x-forwarded-proto": "https"
  }
}
```

Every API has to rely on user input for its operation. But we should do so with care. (Every client could sent a `X-Forwarded-Proto` header. The administrator should take measures to _override_ this header in the SSL offloader.)

For logging alone, the JSON armor prevents format breakouts and log attacks. HTTP request headers and query strings are limited in size. However, you could use [Swagger](/reference/OpenAPI/validation.md) to further validate a parameter's format.

# See also

* [Logging](/administration/logging.md) (Administration)
* [`log` action](/reference/actions/log.md) (Reference)
* [`get-log()` function](/reference/functions/get-log.md) (Reference)
