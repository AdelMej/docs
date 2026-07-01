# slapo-translucent Documentation

## Name and Synopsis

* The `slapo-translucent` is a Translucent Proxy overlay for the OpenLDAP `slapd` daemon used to create a "translucent proxy" by merging data from local and remote LDAP servers.
* Basic Usage:

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapo-translucent` overlay allows for the creation of a hybrid directory service where entries retrieved from a remote LDAP server can have their attributes overridden or augmented by data stored in a local database before being presented to the client. This is typically implemented using a backend such as `slapd-mdb`.

When a search operation is performed, the overlay first populates the results with entries from the remote server. It then applies any overrides defined in the local database. Local modifications (add, modify, and modrdn) are restricted to the root user. Furthermore, for compare operations, the overlay prioritizes attributes defined in the local record before checking the remote database.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| translucent_strict | | If set, attempts to delete attributes in either the local or remote databases will fail with a Constraint Violation instead of being silently ignored. |
| translucent_no_glue | | Disables the automatic creation of "glue" records for add or modrdn operations; all parent entries must be created manually. Glue records are still created for modify operations. |
| translucent_local | | Specifies a list of attributes to be searched in the local database when used in a search filter. |
| translucent_remote | | Specifies a list of attributes to be searched in the remote database when used in a search filter. |
| translucent_bind_local | | If enabled, the server will look for locally stored credentials for simple binds if the attempt to bind to the remote database fails. |
| translucent_pwmod_local | | Enables RFC 3062 Password Modification on locally stored credentials for entries that exist in the remote database. |

---

## Configuration / Files Modified

* **Configuration File:** `/usr/local/etc/openldap/slapd.conf`
* **Database Logic:** The overlay modifies the presentation of results from a proxied database (remote server) by merging them with local database records.

---

## Examples

* Basic configuration for a translucent proxy:

```bash
overlay translucent
translucent_strict
```

* Configuring specific local and remote attributes for search filters:

```bash
translucent_local cn,mail
translucent_remote uid,employeeNumber
```

* Enabling local credential lookups for binds:

```bash
translucent_bind_local
```

---

## Exit Values

* **General behavior:** The overlay returns standard LDAP error codes. Constraint Violations are returned if `translucent_strict` is enabled and an attribute deletion is attempted.

---

## Caveats / Notes

* **Schema Checking:** The Translucent Proxy overlay disables schema checking in the local database so that entries containing only overlay attributes do not need to adhere to the full schema.
* **Suffix Requirement:** Because this overlay does not perform DN rewrites, both the local and remote database instances must share the same suffix. Failure to match suffixes will likely result in "No Such Object" errors.
* **Access Control:** Access control is delegated to the remote DSA for read operations (based on the remote portion of the data) and to either the remote DSA or the local backend for authentication and write operations.
* **Glue Records:** When `translucent_no_glue` is set, users must manually ensure that all parent entries exist before adding a new entry to the local database.
