# slapd-passwd Documentation

**Description:** A backend for slapd that serves user account information from the system's passwd file.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapd-passwd` backend is designed to serve up user account information listed in the system's `passwd(5)` file. Each entry within this database is assigned a Distinguished Name (DN) following the format `uid=<username>,<suffix>`. This backend is primarily provided for demonstration purposes. Because non-base searches require scanning the entire passwd file, it is best suited for use on hosts with small passwd files to ensure acceptable performance.

---

## Options Reference

| Option | Long Form | Description |
| --- | --- | --- |
| file | - | Specifies an alternate passwd file to use (default is `/etc/passwd`). |

---

## Configuration / Files Modified

- `/usr/local/etc/openldap/slapd.conf`
- `/etc/passwd` (or the path specified in the `file` directive)

---

## Examples

- **Basic configuration with default passwd file:**

```bash
database passwd
```

- **Configuration using a custom passwd file:**

```bash
database passwd
file /etc/custom_passwd
```

---

## Exit Values

- 0: Success (General behavior)
- Non-zero: Error (General behavior)

---

## Caveats / Notes

- **Demonstration Purpose:** This backend is provided for demonstration purposes only and may not be suitable for production environments.
- **Performance Limitation:** Operations that are not base searches will scan the entire passwd file, which can result in performance issues on systems with large user databases.
- **Access Control:** The `slapd-passwd` backend does not honor access control semantics described in `slapd.access(5)`. Only read (=r) access to entry pseudo-attributes and other attribute values returned by search operations is honored by the frontend.
