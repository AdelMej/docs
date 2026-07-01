# slapo-unique Documentation

## Name and Synopsis

* The `slapo-unique` is an Attribute Uniqueness overlay for the OpenLDAP `slapd` daemon that enforces the uniqueness of attributes within a specific scope.
* Basic Usage:

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The primary purpose of the `slapo-unique` overlay is to ensure that attribute values remain unique across a defined subtree or set of objects. When an `add`, `modify`, or `modrdn` operation is performed, the overlay searches the designated scope to verify that the provided attribute values do not already exist in other records. If a duplicate value is found, the request is rejected.

The overlay performs these searches using the database's `rootdn` identity. This ensures that the overlay can inspect all relevant data even if Access Control Lists (ACLs) would otherwise prevent standard users from viewing the entire subtree. Consequently, the database must have a `rootdn` configured for this overlay to function correctly.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| unique_uri | | Configures the base DN, attributes, scope, and filter for uniqueness checking using LDAP URI syntax. Supports keywords "strict" (includes nulls), "ignore" (excludes specific attributes), and "serialize" (forces write completion). |
| unique_base | | Legacy parameter: Defines the base DN for the uniqueness domain. |
| unique_ignore | | Legacy parameter: Lists attributes to be ignored during uniqueness checks. |
| unique_attributes | | Legacy parameter: Lists attributes to be checked for uniqueness. |
| unique_strict | | Legacy parameter: Specifies attributes to have strict uniqueness (including null values). |

---

## Configuration / Files Modified

* **Configuration File:** `/usr/local/etc/openldap/slapd.conf`

---

## Examples

* Enforcing uniqueness on the `cn` attribute for all objects in a subtree where the `sn` starts with 'e':

```bash
overlay unique
unique_uri "ldap:///?cn?sub?(sn=e*)"
```

* Configuring multiple independent uniqueness domains:

```bash
overlay unique
unique_uri "ldap:///?uid?sub?"
unique_uri "ldap:///?mail?sub?"
```

---

## Exit Values

* **General behavior:** The overlay returns standard LDAP error codes. It specifically rejects operations that violate uniqueness constraints.

---

## Caveats / Notes

* **Configuration Conflict:** The `unique_uri` parameter cannot be used at the same time as legacy configuration parameters (`unique_base`, `unique_ignore`, etc.).
* **Replication Bypass:** Uniqueness enforcement is bypassed by replication and operations using the "relax" control. To maintain uniqueness in a replicated environment, the overlay must be configured on all servers accepting writes.
* **Rootdn Requirement:** A `rootdn` must be configured on the database to allow the overlay to perform necessary searches across ACL boundaries.
