# slapo-dynlist Documentation

## Name and Synopsis

The **slapo-dynlist** is a Dynamic List overlay for `slapd` that allows for the expansion of dynamic lists and groups by resolving LDAP URI-valued attributes into corresponding directory entries.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `dynlist` overlay facilitates the expansion of dynamic lists and groups during LDAP operations. When an entry with a specific objectClass (configured in the overlay) is returned, any occurrences of a designated attribute containing LDAP URIs are automatically expanded into the entries they point to.

For dynamic lists, the attributes retrieved from the matching entries are added to the original entry. The system enforces LDAP data model constraints during this process; for example, if a single-value attribute is encountered, only the first value found during expansion is retained. For dynamic groups, the Distinguished Names (DNs) of the matching entries are added to a member attribute in the original entry. Recursion is strictly prohibited to prevent infinite loops. Notably, all dynamic behavior is disabled if the `manageDSAit` control is used by the client, in which case the original URLs are returned instead of expanded entries.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `overlay dynlist` | N/A | Adds the dynlist overlay to the current database or the frontend (if defined before database instantiation). |
| `dynlist-attrset` | N/A | Defines the triggering objectClass (`group-oc`), an optional URI restriction, and the attribute name (`URL-ad`) containing URIs to be expanded. Supports mapping options for lists and member/memberOf logic for groups. |
| `dynlist-simple` | N/A | If set to TRUE, downgrades behavior to OpenLDAP 2.4 standards, disabling memberOf processing, nested group support, and filter evaluation of dynamic values. Default is FALSE. |

---

## Configuration / Files Modified

**Configuration File:**

* `/usr/local/etc/openldap/slapd.conf`

**Configuration Variables:**

* `overlay dynlist`
* `dynlist-attrset`
* `dynlist-simple`

---

## Examples

* **Basic dynamic list expansion (collecting email addresses):**

```bash
overlay dynlist
dynlist-attrset groupOfURLs memberURL mail:mail
```

* **Dynamic group implementation with member attribute:**

```bash
overlay dynlist
dynlist-attrset groupOfURLs memberURL member
```

* **Dynamic group with identity authorization (dgIdentity):**

```bash
overlay dynlist
dynlist-attrset groupOfURLs memberURL member+dgMemberOf
```

---

## Exit Values

* **General behavior:** As a configuration overlay, `slapo-dynlist` does not provide standard command-line exit codes. Configuration errors will result in the failure of the `slapd` service to start.

---

## Caveats / Notes

* **Resource Intensity:** If URI expansion is highly resource-intensive and occurs frequently with predictable patterns, it is recommended to add a proxycache later in the overlay stack.
* **Pagination Bug:** Filtering on dynamic groups may return incomplete results if the search operation utilizes the `pagedResults` control.
* **Recursion:** The overlay does not allow recursive expansion to avoid infinite loops.
* **Authorization:** By default, expansions are performed using the identity of the current LDAP user unless overridden by the `dgIdentity` attribute in the group's entry.
