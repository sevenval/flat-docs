# `auth` Action

The `auth` action implements [HTTP Basic authentication)(https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication) between the client and FLAT.
It is useful to protect APIs under development.

## Syntax

An `auth` action contains a list of `user` elements which have the following attributes:

* `pass="..."` to define a password (required, must not be empty)
* `name="..."` to define a username (optional, defaults to the empty string)


## Usage

If the client has not sent authentication credentials, a `401 Authentication Required`
status is returned and the [flow](../flow.md) is halted. In a subsequent request with credentials matching a configured
user, the action does not halt the flow.

> 📎
> Basic authentication does not provide strong protection, as no encryption is used.
> You should only use it on HTTPS connections.


## Example

```xml
<auth>
  <user pass="top secret" />

  <user name="fred" pass="wilma" />
  <!-- repeated user is ignored -->
  <user name="fred" pass="betty" />
</auth>
```

> 📎
> Only the first match is evaluated if user names are repeated.

## Errors

The following cases are considered fatal and will terminate the request:

* the action has no `user` element
* a `user` has no `pass` attribute
