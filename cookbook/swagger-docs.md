
# Using Swagger UI for API Documentation

FLAT uses [Swagger/OpenAPI
2.0](https://swagger.io/docs/specification/2-0/basic-structure/) as its API
definition format.

One of the advantages of well-known, open standard formats is the tooling provided
by the community. Swagger is supported by many tools. One of these is the
official Swagger-UI that is available as Docker Image
[`swaggerapi/swagger-ui`](https://hub.docker.com/r/swaggerapi/swagger-ui/).

## Documentation

If you want to see a pretty (and interactive!) documentation for you API
definition, start the UI server on your local machine like this:

```bash
$ docker run --rm -p 9002:8080 -v $(pwd)/swagger.yml:/swagger.yaml -e SWAGGER_JSON=/swagger.yaml swaggerapi/swagger-ui
```

(The docker command won't produce any output when you start it.)

Now, point your browser to [http://localhost:9002/](http://localhost:9002/) and
browse your docs!

The command line assumes that you are in your project directory and the API
definition is in `./swagger.yml`. If you have a `swagger.json` or any other
name, simply change the part _left_ of the colon (`:`) in the `-v` argument,
such as `-v $(pwd)/swagger.json:/swagger.yaml`.


## Interactive Mode

The Swagger UI features a _Try it out!_ function that creates API calls for you. You can either copy and paste the `curl` command lines provided, or even perform the fetch right in the browser.

To make that work, however, you need to have `host` and `scheme` settings in your API definition. Your `swagger.yml` could start like this:

```yaml
swagger: '2.0'
host: localhost:8080
schemes:
- http
basePath: /api
…
```

The `host` must point to the your FLAT server. `flat start` defaults to `localhost:8080`. Don't forget to set the `http` scheme.

Now, Swagger UI can create API requests for you. But your browser won't be allowed to fetch those URLs because of [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) restrictions.

FLAT has a handy feature to make that work! Simply list the URL of your swagger-ui docker container in the `cors` config settings in `/conf/config.xml`:

```xml
<config>
  <flat>
    <definition src="../swagger.yml" />
    <cors allowed-origins="http://localhost:9002" />
  </flat>
</config>
```

Finally, you can _try it out_. FLAT will handle the CORS preflight checks with your browser, and you can perform test requests with the swagger UI.
