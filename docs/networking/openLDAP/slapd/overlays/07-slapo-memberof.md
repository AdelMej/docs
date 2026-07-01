# slapo-memberof Documentation

## Name and Synopsis

The **slapo-memberof** is a Reverse Group Membership overlay for `slapd` that automatically maintains "is member of" attributes whenever group entries are modified.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `memberof` overlay automates the maintenance of reverse group memberships within an LDAP directory. Whenever a group entry is modified, the overlay automatically updates the "is member of" attributes of the associated members to ensure they reflect the correct group Distinguished Name (DN). While the `dynlist` overlay can offer similar functionality, `slapo-memberof` is specifically designed for this maintenance task. The update operations performed by this overlay are internal to the server and are not replicated to other servers. In a replication environment, consumer servers must be configured with their own instances of the overlay and must be explicitly configured to exclude the `memberOf` attribute from replication.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `overlay memberof` | N/A | Adds the memberof overlay to the current database. |
| `memberof-group-oc` | N/A | The objectClass that triggers the reverse group membership update (default: groupOfNames). |
| `memberof-member-ad` | N/A | The DN-valued attribute containing the names of members in a group (default: member). |
| `memberof-memberof-ad` | N/A | The DN-valued attribute containing the groups an entry belongs to (default: memberOf). |
| `memberof-dn` | N/A | The DN used as modifiersName for internal modifications performed by the overlay (default: rootdn). |
| `memberof-dangling` | N/A | Determines behavior when encountering dangling references: ignore, drop, or error (default: ignore). |
| `memberof-dangling-error` | N/A | The response code returned in case of violation if memberof-dangling is set to error. |
| `memberof-refint` | N/A | If TRUE, attempts to preserve referential integrity by modifying groups when an entry containing a "is member of" attribute is modified. |
| `memberof-addcheck` | N/A | If TRUE, checks newly added entries for membership in existing groups. Requires dangling behavior to be set to ignore. |

---

## Configuration / Files Modified

**Configuration File:**

* `/usr/local/etc/openldap/slapd.conf`

**Configuration Variables:**

* `overlay memberof`
* `memberof-group-oc`
* `memberof-member-ad`
* `memberof-memberof-ad`
* `memberof-dn`
* `memberof-dangling`
* `memberof-dangling-error`
* `memberof-refint`
* `memberof-addcheck`

---

## Examples

* **Basic overlay activation:**

```bash
overlay memberof
```

* **Customizing group and membership attributes:**

```bash
overlay memberof
memberof-group-oc groupOfNames
memberof-member-ad member
memberof-memberof-ad memberOf
```

---

## Exit Values

* **General behavior:** As a configuration overlay, `slapo-memberof` does not provide standard command-line exit codes. Configuration errors will result in the failure of the `slapd` service to start.

---

## Caveats / Notes

* **Replication:** Because maintenance operations are internal, they are never replicated. Consumer servers must run their own instances of the overlay and exclude the `memberOf` attribute from replication (using `exattrs`).
* **Subtree Renames:** The overlay handles renames of individual groups and members but does not handle changes resulting from subtree renames. For referential integrity during subtree renames, the `slapo-refint` overlay should also be used.
* **Backend Compatibility:** This overlay is primarily intended for use with local storage backends.
* **Add Check Restriction:** If `memberof-addcheck` is enabled (TRUE), the `memberof-dangling` option must remain set to its default value of `ignore`.
