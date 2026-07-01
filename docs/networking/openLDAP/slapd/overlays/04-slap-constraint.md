# slapo-constraint Documentation

## Name and Synopsis

The **slapo-constraint** is an Attribute Constraint Overlay used with `slapd` to ensure that attribute values adhere to specific rules beyond basic LDAP syntax.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The primary purpose of the **slapo-constraint** overlay is to enforce syntactic regularity on string-represented data, such as phone numbers, post codes, and Fully Qualified Domain Names (FQDNs). It ensures that attribute values satisfy one or more defined constraints when an LDAP add, modify, or rename command is executed. The overlay specifically monitors the "add" and "replace" values of modify and rename requests. Notably, no constraints are applied if the operation is performed using the `relax` control.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `constraint_attribute` | N/A | Specifies the constraint for a comma-separated list of attributes. Followed by type, value, and optional extra parameters. |
| `regex` | N/A | A Unix-style regular expression that the attribute value must match. |
| `negregex` | N/A | A Unix-style regular expression that the attribute value must NOT match. |
| `size` | N/A | Enforces a specific limit on the length of an attribute value. |
| `count` | N/A | Limits the total number of values allowed for an attribute. |
| `uri` | N/A | Uses an LDAP URI to perform an internal search; the value must be found in the specified scope. |
| `set` | N/A | Interprets a string using ACL set syntax to construct constraints based on the contents of the entry. |
| `restrict=<uri>` | N/A | An extra parameter used to limit a constraint's application to entries matching a specific base, scope, and filter. |

---

## Configuration / Files Modified

**Configuration File:**

* `/usr/local/etc/openldap/slapd.conf`

**Configuration Variables:**

* `constraint_attribute`

---

## Examples

* **Basic constraints for photos, passwords, and email formatting:**

```bash
overlay constraint
constraint_attribute jpegPhoto size 131072
constraint_attribute userPassword count 3
constraint_attribute mail regex ^[[:alnum:]]+@mydomain.com$
constraint_attribute mail negregex ^[[:alnum:]]+@notallowed.com$
```

* **Advanced constraints using URIs and Set logic:**

```bash
overlay constraint
constraint_attribute title uri ldap:///dc=catalog,dc=example,dc=com?title?sub?(objectClass=titleCatalog)
constraint_attribute cn,sn,givenName set "(this/givenName + [ ] + this/sn) & this/cn" restrict="ldap:///ou=People,dc=example,dc=com??sub?(objectClass=inetOrgPerson)"
```

---

## Exit Values

* **General behavior:** As a configuration overlay, `slapo-constraint` does not provide standard command-line exit codes. Configuration errors will result in the failure of the `slapd` service to start. If a request violates a constraint, the server returns an `LDAP_CONSTRAINT_VIOLATION` error.

---

## Caveats / Notes

* **Relax Control:** Constraints are bypassed entirely if the client issues the `relax` control.
* **URI Restrictions:** When using the `uri` type, the provided URI must not include a hostname and must include a list of attributes to evaluate.
* **Set Logic:** Constraints based on the `set` type allow for complex rules derived from other attributes within the same entry.
* **Validation Failure:** Any attempt to modify an attribute that fails to meet the specified constraints will be rejected by the server.
