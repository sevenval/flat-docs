# Docker

The _FLAT Server_ is publicly available as a [Docker](https://www.docker.com) image:
https://hub.docker.com/r/sevenvaltechnologies/flatrunner
So, make sure you have Docker installed on your system.

## Images

The [`latest`](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags?name=latest)
image provides you with the most recent official release.
A [`beta`](https://hub.docker.com/r/sevenvaltechnologies/flatrunner/tags?name=beta)
image with brand-new [features](/CHANGELOG.md) is also available.

You can use the [`flat`](/reference/flat-cli.md) command line tool
to pull the latest release or beta image:

```sh
$ flat pull
```

or

```sh
$ flat pull -b
```

## Container

To run the server, simply start the Docker container with the
[`flat`](/reference/flat-cli.md) CLI:

```sh
$ flat start
```

Add the `-b` option to start a container based on the `beta` release:

```sh
$ flat start -b
```

To use a specific image, provide the respective release number as `TAG`
environment variable, for example

```sh
$ TAG=20200409 flat start
```
