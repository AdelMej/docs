# slapo-rwm Documentation

## Name and Synopsis

* The `slapo-rwm` is a rewrite/remap overlay for the OpenLDAP `slapd` daemon that performs DN/data rewrites and objectClass/attributeType mapping.
* Basic Usage:

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `rwm` overlay is primarily intended to provide virtual views of existing data, whether those sources are remote (in conjunction with the proxy backend) or local (in conjunction with the relay backend). It allows for the translation of schemas between different systems by mapping attribute types and object classes from a foreign set to a local set.

The overlay employs a sophisticated rewriting engine based on POSIX extended regular expressions. This engine supports complex operations such as variable substitution, map resolution, and recursive rule application. It can also perform "suffix massaging," which automatically rewrites the naming context portions of DNs for various LDAP operations (such as bind, search, and modify) between virtual and real contexts.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| rwm-rewriteEngine | | Toggles the rewriting functionality; set to `on` to enable or `off` to disable. |
| rwm-map | | Maps objectClasses and attributeTypes between local and foreign sets. |
| rwm-normalize-mapped-attrs | | If set to "yes", attempts to normalize values from attribute types unknown to the local server. |
| rwm-drop-unrequested-attrs | | If set to "yes", drops attributes that were not explicitly requested in a search operation. |
| rwm-suffixmassage | | Rewrites the trailing part of DNs between virtual and real naming contexts for various operations. |
| rwm-rewriteContext | | Defines a named set of rewrite rules associated with specific server operations (e.g., bindDN, searchFilter). |
| rwm-rewriteRule | | Defines a specific rewriting rule using a regex pattern, a substitution pattern, and optional action flags. |
| rwm-rewriteMap | | Defines maps (LDAP, slapd, or escape) that transform substring rewriting into other values. |
| rwm-rewriteParam | | Sets a global constant parameter that can be dereferenced during rewriting. |
| rwm-rewriteMaxPasses | | Sets the maximum number of total rewriting passes allowed per operation to prevent infinite loops. |

---

## Configuration / Files Modified

* **Configuration File:** `/usr/local/etc/openldap/slapd.conf`

---

## Examples

* Basic suffix massaging for a virtual naming context:

```bash
rwm-rewriteEngine on
rwm-rewriteContext default
rwm-rewriteRule "(.+,)?<virtualnamingcontext>$" "$1<realnamingcontext>" ":"
```

* Mapping object classes and attributes from a foreign server:

```bash
map objectclass groupOfNames groupOfUniqueNames
map attribute uniqueMember member
map attribute cn *
map attribute sn *
map attribute manager *
map attribute description *
map attribute *
```

* Using a map to transform DN values into search filters:

```bash
rwm-rewriteMap escape dn2filter unescapedn escape2filter
rwm-rewriteContext bindDN
rwm-rewriteRule "^(mail=)([^,]+@[^,]+)$" "${attr2dn($1${dn2filter($2)})}" ":@I"
```

---

## Exit Values

* **General behavior:** The overlay returns standard LDAP error codes. If a rule matches and includes a specific return code flag (e.g., `U{n}`), the specified code is returned. If a rule is designed to stop an operation, it issues an "unwilling to perform" error.

---

## Caveats / Notes

* **Experimental Status:** This overlay is considered experimental and should be used with caution.
* **DN Syntax Awareness:** When mapping DN-valued attributes from local to remote, the local attribute type must be appropriately defined as using the distinguishedName syntax.
* **Naming Context Restrictions:** Rules that rewrite empty DNs ("") or the subschemaSubentry DN (usually "cn=subschema") will prevent clients from reading the root DSE or the DSA's schema.
* **Complex Types:** Certain DN-related syntaxes (e.g., compound types like nameAndOptionalUID) are currently not rewritten by the overlay.
