# slapo-accesslog Documentation

## Name and Synopsis

The **slapo-accesslog** is an Access Logging overlay used with `slapd` to record all accesses to a specific backend database onto a separate logging database.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The primary purpose of the slapo-accesslog overlay is to allow administrators to review all activity on a given database using arbitrary LDAP queries rather than relying solely on local flat text files. The overlay handles the automated recording of operations and provides mechanisms to prune older log records automatically to manage storage. Records are stored using a specific "audit" schema to ensure they remain readable whether viewed as LDIF or in raw form.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `logdb` | N/A | Specifies the suffix of the database used to store log records. The database must support ordered returns (e.g., slapd-mdb). |
| `logops` | N/A | Specifies operation types to log: abandon, add, bind, compare, delete, extended, modify, modrdn, search, and unbind. |
| `logbase` | N/A | Specifies a set of operations that are only logged if they occur under a specific subtree of the database. |
| `logold` | N/A | Specifies a filter for matching Deleted and Modified entries to log their old contents along with the current request. |
| `logoldattr` | N/A | Lists attributes whose old contents are always logged in Modify and ModRDN requests matching `logold` filters. |
| `logpurge` | N/A | Specifies the maximum age for log entries and the interval for scanning the database to delete old entries. |
| `logsuccess` | N/A | If TRUE, only successful requests (LDAP_SUCCESS) are logged. Default is FALSE. |

---

## Configuration / Files Modified

**Configuration File:**

* `/usr/local/etc/openldap/slapd.conf`

**Configuration Variables:**
The overlay options must appear in the configuration file immediately after the `overlay accesslog` directive.

---

## Examples

* **Basic logging of writes and reads for a specific subtree with old entry logging:**

```bash
database mdb
suffix dc=example,dc=com
...
overlay accesslog
logdb cn=log
logops writes reads
logbase search\|compare ou=testing,dc=example,dc=com
logold (objectclass=person)
```

* **Logging all operations with ordered indexing for performance:**

```bash
database mdb
suffix cn=log
...
index reqStart eq
access to *
  by dn.base="cn=admin,dc=example,dc=com" read
```

---

## Exit Values

* **General behavior:** As this is a configuration overlay and not a standalone command-line utility, it does not have standard exit codes. Configuration errors will typically result in `slapd` failing to start or the overlay failing to load.

---

## Caveats / Notes

* **Database Requirements:** The database specified in `logdb` must support an ordered return of results.
* **Performance:** When using a log database that supports ordered indexing on generalizedTime attributes, specifying an `eq` index on the `reqStart` attribute significantly improves purge operation performance.
* **Schema Status:** The "audit" schema used by this overlay is a work in progress and subject to change without notice.
* **Security:** Access controls on the log database should be configured to prevent general access to sensitive audit data.
