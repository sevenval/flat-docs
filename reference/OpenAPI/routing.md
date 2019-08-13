# Routing

An API definition must provide a list of callable _paths_ – sometimes also called _routes_ or _endpoints_.

In `swagger.yaml` this is defined in the `paths` section:

```yaml
swagger: "2.0"
info:
  description: "…"
  version: "1.0.0"
  title: "My API"
basePath: "/v1"
paths:
  /users:
    get:
      responses:
        200:
          description: "Get a list of all users"
    post:
      responses:
        201:
          description: "User created"
  /dashboard:
    get:
      …
```

One important setting is the `basePath` property. All described API paths are only accessible below that `basePath`.

Furthermore, only the paths that are explicitly defined are permissible, i.e. they may not be extended arbitrarily.

In our example, the following paths are valid:

```
/v1/users
/v1/dashboard
```

While these are undefined (especially those pointing outside of base path):
```
/v1
/users
/v1/users/too/long
```

The Swagger docs have more information on [paths and operations](https://swagger.io/docs/specification/2-0/paths-and-operations/).

## Assigning FLAT Flows

In FLAT you can assign a [flow](/reference/flow.md) file to every API path to define the request processing. This is done with the `x-flat-flow` property:

```yaml
paths:
  /users:
    get:
      # flow for GET /users
      x-flat-flow: api/users.xml
      responses:
        200:
          description: "Get a list of all users"
    post:
      # flow for POST /users
      x-flat-flow: api/create-user.xml
      responses:
        201:
          description: "User created"
  /dashboard:
    # flow for /dashboard (all methods)
    x-flat-flow: api/dashboard.xml
    get:
      …
```

You can specify a flow for a path (as in `/dashboard`) or for specific methods/operations (as in `/users`). The same flow can be used for multiple paths or methods.

### Fallback Flow

An `x-flat-flow` property set directly in the `paths` object behaves as a _fallback flow_. If the incoming request has matched a defined path, but that path did not define a flow, the fallback flow will be executed:

```yaml
paths:
  x-flat-flow: api/fallback.xml
```

## Init Flow

An _init flow_ is a separate flow file that is executed before the regular [flow](/reference/flow.md)
that is defined for an API path. It is specified by setting `x-flat-init` on
the top level in the OpenAPI definition:

```yaml
…
x-flat-init: init.xml
paths:
  /users:
    get:
…
```

The init flow can be used [extract common initialization
tasks](/cookbook/init-flow.md), e.g. initialize variables or set HTTP response
headers.

For requests outside of the API `basePath` (e.g. `/` or `/assets`), the init
flow is _not_ executed. It is only called for API requests.

A [`break`](#break) statement in the init flow terminates the **whole
request**; the regular flow (specified by `x-flat-flow`) is _not_ executed. A
[`return`](#return) statement terminates only the init flow; the regular flow
is executed. Terminating [actions](actions/README.md) like
[`echo`](actions/echo.md) or [`dump`](actions/dump.md) will prevent the actual
flow being executed, too.


## Path Parameters

Swagger Paths can define path parameters:

```
paths:
  /users/{userid}:
    x-flat-flow: api/user-info.xml
    …
```

All named params are available from inside the flow in the pre-defined [variable `$request`](/reference/variables.md#predefined-variables) as properties of the `$request/params` object:

```xml
<template>
{
  "user": {
    "name": {{ $request/params/userid }}
  }
}
</template>
