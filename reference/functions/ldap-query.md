# `ldap-query()`

```
OXN-node-set ldap-query(string url, string rdn, string rdnPassword, string base_dn, string search, string attributes)
```

The `ldap-query()` function connects to an LDAP server with the given `url`, `rdn` and `rdnPassword`.
It then performs a query by the given `search`.
An [OXN](/reference/templating/oxn.md) JSON array is returned with objects containing the found entities' `dn` and additional attributes given by `attributes`.
If no entities match the query an empty node-set is returned.

## Parameters

* `url` The ldap URL (string)
* `rdn` The (relative) distinguished name of the connecting user (string)
* `rdnPassword` The password of the connecting user (string)
* `base_dn` The base distinguished name for the directory, used for the search (string)
* `search` The filter for searching entities (string)
* `attributes` A comma-separated list of attributes to return (string)


## Example

In the following example, the LDAP server is connected with the DN given in the `rdn` and `rdnPassword` POST parameters.
The given filter is used to search for an entry of a person which is a member of a group `Users` and sAMAccountName containg `doe`.
In addition to the (default) `dn`, the `sAMAccountName`, `displayName` and `mail` from the found entries are added to the results.

```xml
<flow>
  <eval out="$search">concat("(&amp;(objectClass=person)(memberOf=CN=Users,ou=People,dc=example,dc=com)(sAMAccountName=*doe*))")</eval>
  <eval out="$attributes">"sAMAccountName,displayName,mail"</eval>

  <eval out="$ldap">ldap-lookup($ldap_settings/url, $request/post/rdn, $request/post/rdnPassword, "dc=example,dc=com", $search, $attributes)</eval>
  <error if="not($ldap)">
  {
    "status": 403,
    "message": "ldap-lookup() failed"
  }
  </error>
</flow>
```

The result is
```json
[
  {
    "dn": "cn=John Doe,ou=People,dc=example,dc=com",
    "sAMAccountName": "john.doe",
    "displayName": "John Doe",
    "mail": "john.doe@example.com"
  },
  {
    "dn": "cn=Joseph Adoell,ou=People,dc=example,dc=com",
    "sAMAccountName": "joseph.adoell",
    "displayName": "Joseph Adoell",
    "mail": "joseph.adoell@example.com"
  }
]
```
