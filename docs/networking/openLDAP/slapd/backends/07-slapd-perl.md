# slapd-perl Documentation

**Description:** A Perl backend for slapd that embeds a perl(1) interpreter to handle LDAP requests through custom Perl modules.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapd-perl` backend functions by embedding a Perl interpreter directly into the slapd daemon. When a "database perl" section is defined in the configuration file, the administrator must specify which Perl module to use. Slapd then creates a new Perl object to manage all requests for that specific backend instance. To function correctly, the specified Perl module must implement a specific set of methods, including `new`, `search`, `compare`, `modify`, `add`, `modrdn`, `delete`, `config`, and `init`. By default, these methods return the result code to the client; any actions that are not implemented by the module should return the `unwillingToPerform` (53) error code.

---

## Options Reference

| Option | Long Form | Description |
| --- | --- | --- |
| perlModulePath | - | Adds the specified path to the @INC variable for Perl module resolution. |
| perlModule | - | Specifies the module name (ModName.pm) to be used by the backend. |
| filterSearchResults | - | If set, search results are treated as candidates to be filtered by the request filter rather than being returned directly to the client. |
| perlModuleConfig | - | Invokes the module's specific configuration method with the provided arguments. |

---

## Configuration / Files Modified

- `/usr/local/etc/openldap/slapd.conf`
- **Requirement:** Options must follow a "database perl" line and appear before any subsequent "backend" or "database" lines.

---

## Examples

- **Example Module:**
The OpenLDAP source tree includes a `SampleLDAP` module within the `slapd/back-perl/` directory for reference.

---

## Exit Values

- 0: Success (General behavior)
- Non-zero: Error (General behavior)

---

## Caveats / Notes

- **Access Control:** This backend does not honor standard access control semantics described in `slapd.access(5)`. All access control logic is delegated to the underlying Perl scripting. The frontend only honors read (=r) access for entry pseudo-attributes and other attribute values returned by search operations.
- **Interface Stability:** The interface between this backend and the Perl module may change over time.
- **Deprecated Behavior:** Previously, unrecognized lines in the configuration file were passed to the module's config method. This behavior is deprecated; administrators should use the `perlModuleConfig` directive instead.
