# slapd-null Documentation

**Description:** A null backend for slapd that mimics the behavior of the `/dev/null` device.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapd-null` backend is designed to perform operations without producing any actual results or making changes, functioning similarly to a `/dev/null` device. Search requests are processed successfully but return no entries. Comparison operations result in a "false" status. While updates return a success code (unless the database is set to read-only), no modifications are actually performed. By default, any bind attempts for DNs other than the rootdn will fail unless the specific bind option is enabled.

---

## Options Reference

| Option | Long Form | Description |
| --- | --- | --- |
| bind | - | Allows binds as any DN within this backend's suffix using any password. Default is "off". |
| dosearch | - | If enabled, a single entry will be returned for all search requests. The DN of this entry will match the database suffix. Default is "off". |

---

## Configuration / Files Modified

- `/usr/local/etc/openldap/slapd.conf`
- **Note:** Configuration options must follow a "database null" line and appear before any subsequent "backend" or "database" lines.

---

## Examples

- **Basic configuration with bind enabled:**

```bash
database null
suffix "cn=Nothing"
bind on
```

---

## Exit Values

- 0: Success (General behavior)
- Non-zero: Error (General behavior)

---

## Caveats / Notes

- **Access Control:** The null backend does not honor any of the access control semantics described in `slapd.access(5)`.
- **Update Behavior:** Although updates return success, they are essentially no-ops and do not modify data.
