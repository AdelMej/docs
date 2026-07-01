# slapo-refint Documentation

## Name and Synopsis

* The `slapo-refint` is a Referential Integrity overlay for the OpenLDAP `slapd` daemon, used to maintain schema cohesiveness when utilizing reference attributes.
* Basic Usage:

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapo-refint` overlay ensures that database records remain consistent when entries containing reference attributes are modified or deleted. When an entry is deleted or renamed, the overlay automatically performs a search for all other records containing those specific references and updates them accordingly. For example, if a "manager" attribute is configured for integrity, deleting a person's record will trigger the overlay to remove that DN from any other entries where they were listed as the manager. If a record is renamed, the overlay replaces the old DN with the new one across all relevant attributes.

This overlay requires a `rootdn` to be configured on the database, as the module runs with `rootdn` privileges to perform its internal updates.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| refint_attributes | | Specifies one or more attributes for which referential integrity is maintained. |
| refint_nothing | | Specifies a placeholder DN to be used when the last value of an attribute would otherwise be deleted. This prevents Object Class Violations in schemas where an attribute must exist. |
| refint_modifiersname | | The DN to be used as the `modifiersName` for internal modifications performed by the overlay. Defaults to "cn=Referential Integrity Overlay". |

---

## Configuration / Files Modified

* **Configuration File:** `/usr/local/etc/openldap/slapd.conf`
* The overlay performs internal modifications to attributes specified in `refint_attributes`.

---

## Examples

* Basic configuration to maintain integrity for the "manager" attribute:

```bash
overlay refint
refint_attributes manager
```

* Configuration with a placeholder to prevent Object Class Violations:

```bash
overlay refint
refint_attributes manager
refint_nothing "cn=None,ou=Placeholders,dc=example,dc=com"
```

---

## Exit Values

* **General behavior:** The overlay returns standard LDAP error codes for operations that fail to meet schema requirements or internal processing errors.

---

## Caveats / Notes

* **Replication Warning:** Modifications performed by this overlay are not automatically propagated during replication. To maintain synchronization, the overlay must be configured identically on all replication consumers.
* **Rootdn Requirement:** A `rootdn` must be set for the database to allow the overlay to perform necessary updates.
* **Schema Requirements:** If an attribute is required by an Object Class but its last reference is deleted, the operation will fail unless `refint_nothing` is configured with a valid DN.
