# slapo-syncprov Documentation

## Name and Synopsis

* The `slapo-syncprov` is a Sync Provider overlay for the OpenLDAP `slapd` daemon that implements provider-side support for LDAP Content Synchronization (RFC4533) and syncrepl replication.
* Basic Usage:

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapo-syncprov` overlay provides the necessary infrastructure for LDAP Content Synchronization and syncrepl replication. It is compatible with any backend database that maintains the `entryCSN` and `entryUUID` attributes for its entries. The overlay automatically creates a `contextCSN` attribute in the root entry of the database. To maintain high performance and reduce database contention, the `contextCSN` is updated only in memory during write operations; these values are persisted to the database upon server shutdown and reloaded into memory at startup. For systems requiring faster recovery from unclean shutdowns, checkpointing can be configured to periodically commit the `contextCSN` to the physical database.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| syncprov-checkpoint | | Defines a threshold of write operations or elapsed minutes after which the `contextCSN` is written to the underlying database. Checkpointing is disabled by default. |
| syncprov-sessionlog | | Configures an in-memory session log to record information about write operations (excluding Adds). The value specifies the number of operations to record. |
| syncprov-sessionlog-source | | Specifies a DN for using accesslog as the session log source instead of an in-memory log. This is mutually exclusive with `syncprov-sessionlog`. |
| syncprov-nopresent | | If set to TRUE, the Present phase of refreshing is skipped. This should only be used when `syncprov` is configured on top of a log database (such as one managed by the `accesslog` overlay). Default is FALSE. |
| syncprov-reloadhint | | Specifies whether the overlay should honor the `reloadHint` flag in the Sync Control. Must be set to TRUE when using the `accesslog` overlay for delta-based syncrepl replication support. Default is FALSE. |

---

## Configuration / Files Modified

* **Configuration File:** `/usr/local/etc/openldap/slapd.conf`
* **Database Modification:** Creates a `contextCSN` attribute in the root entry of the database.

---

## Examples

* Enabling checkpoints after 100 write operations or 60 minutes:

    ```bash# slapo-syncprov Command Documentation

## Name and Synopsis

* The `slapo-syncprov` is a Sync Provider overlay for the OpenLDAP `slapd` daemon that implements provider-side support for LDAP Content Synchronization (RFC4533) and syncrepl replication.
* Basic Usage:

```bash
/usr/local/etc/openldap/slapd.conf
```
