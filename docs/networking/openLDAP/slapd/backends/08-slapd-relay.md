# slapd-relay Documentation

**Description:** A relay backend for slapd that maps a naming context from a database into a virtual naming context, supporting attributeType and objectClass manipulation.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapd-relay` backend is designed to map a naming context defined in a database running within the same slapd instance into a virtual naming context. It allows for the manipulation of attributeTypes and objectClasses during this process. This backend requires the `slapo-rwm(5)` overlay to function correctly. Note that both the relay backend and the required overlay are considered experimental.

---

## Options Reference

| Option | Long Form | Description |
| --- | --- | --- |
| relay | - | Specifies the real naming context of the database that is presented under a virtual naming context. |
| suffix | - | Defines the suffix for the relay database (the only database option allowed by this backend). |

---

## Configuration / Files Modified

- `/usr/local/etc/openldap/slapd.conf`

---

## Examples

- **Plain virtual naming context mapping:**

```bash
database                relay
suffix                  "dc=virtual,dc=naming,dc=context"
relay                   "dc=real,dc=naming,dc=context"
overlay                 rwm
rwm-suffixmassage       "dc=real,dc=naming,dc=context"
```

- **Mapping that looks up the real naming context for each operation:**

```bash
database                relay
suffix                  "dc=virtual,dc=naming,dc=context"
overlay                 rwm
rwm-suffixmassage       "dc=real,dc=naming,dc=context"
```

- **Old-fashioned suffixalias mapping:**

```bash
database                relay
suffix                  "dc=virtual,dc=naming,dc=context"
relay                   "dc=real,dc=naming,dc=context"
overlay                 rwm
rwm-rewriteEngine       on
rwm-rewriteContext      default
rwm-rewriteRule         "dc=virtual,dc=naming,dc=context"
                                 "dc=real,dc=naming,dc=context" ":@"
rwm-rewriteContext      searchFilter
rwm-rewriteContext      searchEntryDN
rwm-rewriteContext      searchAttrDN
rwm-rewriteContext      matchedDN
```

---

## Exit Values

- 0: Success (General behavior)
- Non-zero: Error (General behavior)

---

## Caveats / Notes

- **Experimental Status:** Both the `slapd-relay` backend and the `slapo-rwm(5)` overlay are experimental.
- **Name Massaging:** The relay database does not automatically rewrite naming contexts; the `slapo-rwm(5)` overlay must be explicitly instantiated and configured for this purpose.
- **Access Control:** Access rules are based on the identity that issued the operation. Because the backend bypasses standard frontend operations, the relay database must provide its own access rules consistent with the original database, using identities in the real naming context.
