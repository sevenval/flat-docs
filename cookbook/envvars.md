# Using Environment Variables

It is good practice to make a FLAT app configurable so that it can run in multiple environments. For example, the upstream APIs have different locations in a local dev setup and the production system.

FLAT runs as a _Docker Container_ and can make use of the _environment variables_ defined in `Dockerfile`, `docker-compose.yml` or with `docker run -e`.


## Defining Env Vars

### `flat` cli

In a development setup we may use [`flat` cli](/reference/flat-cli.md) to run our API. By convention, all shell environment variables starting with `FLAT_` are passed into the container.

Let's start FLAT with an env variable:

```bash
$ export FLAT_MY_VAR="my value"
$ flat start
```

(We assume, that you have a `swagger.yaml` and some API path that you can call. If you're not so far, yet, checkout the [tutorial](/tutorial/README.md)).

The env vars can be inspected with the `env` [debug topic](/reference/debugging.md):

```bash
$ curl -s -H debug:env localhost:8080/ > /dev/null
debug   [env]                  Env vars: {
    "HOSTNAME": "cee6f96cd5ac",
    "FLAT_DEBUG": ":error:log",
    "PATH": "…",
    "PWD": "…",
    "FLAT_MY_VAR": "my value",
    "SHLVL": 1,
    "HOME": "/home/flat"
}
```

There's our `FLAT_MY_VAR`! (The `FLAT_DEBUG` var is set by `flat` cli to allow this kind of debugging).

Note that we have discarded the actual HTTP response with `> /dev/null`. The default debug sink in `flat` is `stderr` which is printed on the starting shell. If you have no idea where this terminal window is, you can also include the debug output in the HTTP response:

```bash
$ curl -s -H debug:env::inline localhost:8080
```

The `inline` sink interweaves the debug output with your API response. It will disturb your response, but it is sometimes handy for debugging…

### Docker

If you control the Docker setup yourself (e.g. `docker-compose`, Kubernetes, …) you can use its built-in support for environment variables. In this case, you are not bound to variable names starting with `FLAT_`.

Your `docker-compose.yaml` could look like this:
```yaml
version: '3'
services:
  flat:
    image: sevenvaltechnologies/flatrunner
    volumes:
      - .:/app
    ports:
      - 8080:8080
    environment:
      - MY_VAR=my value
```

We have defined `MY_VAR` in the `environment` section. Start the container with

```bash
$ docker-compose up
```

and run the `curl` command again:

```bash
$ curl -s -H debug:env::inline localhost:8080
```

This time the debug output will appear in the `docker-compose` output. If your container is running in the background, you may start another log tail with

```bash
$ docker-compose logs -f
```

Between a bunch of access logs, you will see the current environment dump:

```
flat_1  | … debug   [env]                  Env vars: {
flat_1  |     "HOSTNAME": "1a315a77a2ed",
flat_1  |     "MY_VAR": "my value",
flat_1  |     "PATH": "…",
flat_1  |     "PWD": "…",
flat_1  |     "SHLVL": 1,
flat_1  |     "HOME": "/home/flat"
flat_1  | }
```

Another way of defining env vars would be the `-e` flag of a simple `docker run`:

```bash
$ docker run --rm -it -e MY_VAR="my value" -v "$(pwd):/app" sevenvaltechnologies/flatrunner
```

Great! We now know how to define environment variables. Let's use them!

## Accessing Env Vars in Flows

All environment variables are accessible from [flows](/reference/flow.md) by the system [variable `$env`](/reference/variables.md). It is an [OXN](/reference/templating/oxn.md) object with the defined environment variables as properties:

```json
{
  "MY_VAR": "my value",
  …
}
```

We could create a simple [template](/reference/actions/template.md) to send our env var back to the client:

```xml
<flow>
  <template>
  {
    "my var": {{ $env/MY_VAR }}
  }
  </template>
</flow>
```

## Use Cases

### Upstream URLs

A typical use-case for an env var is the base URL of an upstream API.

Let's define the origin of our auth service:

```bash
AUTH_SERVICE=http://localhost:3000
```

To [request](/reference/actions/request.md) the auth service, we have to build a URL:

```xml
<flow>
  <request>
  {
    "url": {{ concat($env/AUTH_SERVICE, "/v1/logout" )}},
    "headers": {
      {{: $request/headers/authorization }}
    }
  }
  </request>
</flow>
```

If the app is deployed into production, the `AUTH_SERVICE` variable will be set to another value, probably one with `https` and running on a different port.

## JWT

When working with [JWT](https://en.wikipedia.org/wiki/JSON_Web_Token) a signing key (or two, in case of `RS*` algos) need to be configured in FLAT. It is good idea to pass these as environment variables.

See [Working with JWT](/cookbook/jwt.md) for a larger example.

## Credentials

Often, access between services is restricted by an [HTTP Authentication](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication).

You could read Basic Auth credentials from an env var in your [request](/reference/actions/request.md#options):

```xml
<flow>
  <request>
  {
    "url": "…",
    "options": {
      "basic-auth-credentials": {{ $env/AUTH_SERVICE_CREDS }}
    }
  }
  </request>
</flow>
```

Or you need to include a token in your requests:

```xml
<flow>
  <request>
  {
    "url": "…",
    "headers": {
      "authentication": {{ concat("Bearer ", $env/AUTH_SERVICE_TOKEN) }}
    }
  }
  </request>
</flow>
```

## See also

* Test Action [`set-env`](/reference/actions/set-env.md) (reference)
* [Working with JWT](/cookbook/jwt.md) (cookbook)
