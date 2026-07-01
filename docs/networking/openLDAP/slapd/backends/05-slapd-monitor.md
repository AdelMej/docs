# slapd-monitor Documentation

**Description:** A monitor backend for slapd that provides information about the running status of the daemon.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapd-monitor` backend is not a standard database; instead, it is automatically generated and dynamically maintained by the slapd daemon. It serves to provide information regarding the current running status of the service. To inspect the monitor information, users must issue a subtree search with the base set to `cn=Monitor`. Since the backend primarily produces operational attributes, queries should request both the `+` and `*` attributes to ensure all relevant data is returned.

---

## Options Reference

| Option | Long Form | Description |
| --- | --- | --- |
| database monitor | - | Instantiates the monitor database. Only one occurrence is permitted in the configuration file. |

*Note: The monitor database honors standard `rootdn`, `rootpw`, and ACL directives defined in the main slapd configuration.*

---

## Configuration / Files Modified

- `/usr/local/etc/openldap/slapd.conf`
- **Requirement:** The backend must be enabled during configuration using `configure --enable-monitor`.
- **Requirement:** The `core.schema` file must be loaded, as the monitor backend relies on standard track attributeTypes defined therein.

---

## Examples

- **Activating the database in the configuration file:**

```bash
database monitor
```

- **Example Access Control List (ACL):**

```bash
access to dn.subtree="cn=Monitor"
     by dn.exact="uid=Admin,dc=my,dc=org" write
     by users read
     by * none
```

---

## Exit Values

- 0: Success (General behavior)
- Non-zero: Error (General behavior)

---

## Caveats / Notes

- **Search Limits:** The monitor backend does not honor size or time limits during search operations.
- **Suffix Restriction:** The suffix for this database is automatically set to `cn=Monitor` and cannot be explicitly set or modified using the `suffix` directive.
