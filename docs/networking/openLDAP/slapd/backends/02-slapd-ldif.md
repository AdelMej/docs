# slapd-ldif Documentation

**Description:** A basic storage backend for slapd that stores entries in text files using LDIF format.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapd-ldif` backend is a storage solution that saves database entries as text files in LDIF format. It leverages the filesystem to construct and maintain the database's tree structure. This backend is designed for scenarios where an easy-to-use and cost-effective storage method is preferred over high performance, providing a permanent storage layer for higher-level internal structures.

---

## Options Reference

| Option | Long Form | Description |
| --- | --- | --- |
| directory | - | The path to the directory where the database tree starts. This directory must exist and have read/write/execute (rwx) permissions for the user running slapd. |

---

## Configuration / Files Modified

- `/usr/local/etc/openldap/slapd.conf`

---

## Examples

- Specifying a custom storage directory:

```bash
directory /var/lib/ldap/ldif_data
```

---

## Exit Values

- 0: Success (General behavior)
- Non-zero: Error (General behavior)

---

## Caveats / Notes

- **Performance:** This backend is intended for low performance and should not be used in high-demand production environments.
- **Access Control:** The LDIF backend does not support the access control semantics defined in `slapd.access(5)`. Only read (=r) access to entry pseudo-attributes and other attribute values returned by search operations is honored by the frontend.
