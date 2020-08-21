# `ldap-lookup()`

```
OXN-node-set ldap-lookup(string url, string rdn, string rdnPassword, string base_dn, string userSearch, string userPassword, string attributes)
```

The `ldap-lookup()` function connects to an LDAP server with the given `url`, `rdn` and `rdnPassword`.
It then searches for a user by the given `userSearch`.
If a user is found, it connects with the user's DN and the given `userPassword`.
If the password is correct, an [OXN](/reference/templating/oxn.md) JSON document is returned with at least the user's `dn` and additional attributes given by `attributes`.
Otherwise an empty node-set is returned.

## Parameters

* `url` The ldap URL (string)
* `rdn` The (relative) distinguished name of the (system) user (string)
* `rdnPassword` The password of the (system) user (string)
* `base_dn` The base distinguished name for the search (string)
* `userSearch` The filter for searching a user (string)
* `userPassword` The user's password (string)
* `attributes` A comma-separated list of attributes to return (string)


## Example

In the following example, the LDAP server is connected with the DN given in `$ldap_settings/bind_dn` and the password from $env/FLAT_SYSTEM_PASSWORD.
Zhe given filter is used to search for an entry of a person which is a member of a group "Users" and has the email address "john.doe@example.com".
In addition to the (default) `dn`, the `sAMAccountName` and `mail` from the entry are added to the result.

```xml
<flow>
  <eval out="$userSearch">concat("(&amp;(objectClass=person)(memberOf=CN=Users,ou=People,dc=example,dc=com)(mail=john.doe@example.com))")</eval>
  <eval out="$attributes">"sAMAccountName,mail"</eval>

  <eval out="$ldap">ldap-lookup($ldap_settings/url, $ldap_settings/bind_dn, $env/FLAT_SYSTEM_PASSWORD, $ldap_settings/base_dn, $userSearch, "myP4s5w0rD, $attributes)</eval>
  <error if="not($ldap)">
  {
    "status": 403,
    "message": "ldap-lookup() failed"
  }
  </error>
</flow>
```
