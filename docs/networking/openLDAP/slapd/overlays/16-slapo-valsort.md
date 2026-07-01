# slapo-valsort Command Documentation

## Name and Synopsis

* The `slapo-valsort` is a Value Sorting overlay for the OpenLDAP `slapd` daemon that automatically sorts the values of specific multi-valued attributes within a designated subtree during search responses.
* Basic Usage:

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The primary purpose of the `slapo-valsort` overlay is to organize the order of values for multi-valued attributes whenever they are returned in a search response. It supports four sorting methods: alpha-ascend, alpha-descend, numeric-ascend, and numeric-descend.

Additionally, the overlay supports a "weighted" sort. This method uses a numeric weight prepended to the attribute values. The weighted sort is always performed in ascending order; however, if multiple values share the same weight, a secondary sort method can be applied to break the tie. By default, these weighting factors are stripped and not returned in search results. To see the weights, the client must request them using the `valsort` control with a specific "{b}" encoding.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| valsort-attr | | Configures sorting for a specified attribute within a base DN. Syntax: `<attribute> <baseDN> (<sort-method> \| weighted [<sort-method>])`. Supported methods are alpha-ascend, alpha-descend, numeric-ascend, and numeric-descend. |

---

## Configuration / Files Modified

* **Configuration File:** `/usr/local/etc/openldap/slapd.conf`

---

## Examples

* Basic configuration for alpha-ascending sort on the "member" attribute:

```bash
overlay valsort
valsort-attr member ou=groups,dc=example,dc=com alpha-ascend
```

* Invoking `ldapsearch` with the `valsort` control (using base64 encoding for the required "{b}" value):

```bash
ldapsearch -E 1.3.6.1.4.1.4203.666.5.14=::MAMBAf8=
```

---

## Exit Values

* **General behavior:** The overlay returns standard LDAP error codes for operations that fail to meet configuration requirements or encounter processing errors.

---

## Caveats / Notes

* **Syntax Compatibility:** It is an error to specify an alphanumeric sort method (alpha-ascend/descend) for attributes with Integer or NumericString syntax.
* **Numeric Requirement:** It is an error to specify a numeric sort method (numeric-ascend/descend) for attributes that do not have an Integer or NumericString syntax.
* **Weighted Sort Behavior:** The weighted sort is always performed in ascending order, though the secondary sort method can be either ascending or descending.
