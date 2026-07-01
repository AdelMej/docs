# slapd-sql Documentation

**Description:** An SQL backend for slapd that presents information stored in a Relational Database Management System (RDBMS) as an LDAP subtree without requiring custom programming.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapd-sql` backend allows users to expose RDBMS data to modern solutions that expect information in LDAP format, such as for user authentication or email lookups. It functions by using meta-information to translate LDAP queries into SQL queries while leaving the original relational schema untouched, enabling interoperability between SQL and LDAP applications without replication. The backend is highly configurable across different SQL dialects and connects to databases via ODBC, making it suitable for integration in heterogeneous environments. This backend is currently experimental and is intended as a gateway to existing RDBMS storage rather than a general-purpose replacement for the standard MDB backend.

---

## Options Reference

| Option | Long Form | Description |
| --- | --- | --- |
| dbname | - | The name of the ODBC datasource to use. |
| dbhost | - | The hostname for the database (overrides datasource settings). |
| dbpasswd | - | The password for the database (overrides datasource settings). |
| dbuser | - | The username for the database (overrides datasource settings). |
| subtree_cond | - | Where-clause template used to form a subtree search condition. |
| children_cond | - | Where-clause template used to form a children search condition. |
| use_subtree_shortcut | - | If set to yes, do not use the subtree condition when the searchBase is the database suffix and the scope is subtree. |
| oc_query | - | SQL query used to collect objectClass mapping data. |
| at_query | - | SQL query used to collect attributeType mapping data. |
| id_query | - | SQL query used to map a DN to an entry in the ldap_entries table. |
| insentry_stmt | - | SQL statement used to insert a new entry in the ldap_entries table. |
| delentry_stmt | - | SQL statement used to delete an existing entry from the ldap_entries table. |
| delobjclasses_stmt | - | SQL statement used to delete an existing entry's ID from the ldap_objclasses table. |
| upper_func | - | Name of a function that converts a given value to uppercase for case-insensitive matching. |
| upper_needs_cast | - | If set to yes, the upper_func requires an explicit cast when applied to literal strings. |
| strcast_func | - | Name of a function that converts a given value to a string for appropriate ordering. |
| concat_pattern | - | Pattern used to concatenate strings (default is CONCAT(?,?)). |
| aliasing_keyword | - | Defines the keyword used for aliasing (e.g., "AS"). |
| aliasing_quote | - | Defines the quoting character of the aliasing keyword. |
| has_ldapinfo_dn_ru | - | Informs the backend if the dn_ru column is present in the ldap_entries table. |
| fail_if_no_mapping | - | If set to yes, forces attribute write operations to fail if no mapping is available. |
| allow_orphans | - | If set to yes, orphaned entries (without a parent entry) can be added. |
| baseObject | - | Instructs the database to manage an in-memory baseObject entry instead of looking for one in the RDBMS. |
| create_needs_select | - | Instructs whether entry creation needs a subsequent select to collect the automatically assigned ID. |
| fetch_attrs | - | List of attributes that must always be fetched (e.g., for ACL purposes). |
| fetch_all_attrs | - | If set to yes, requires all attributes to be always loaded. |
| check_schema | - | If set to yes, checks schema adherence after modifications. |
| sqllayer | - | Loads a specific layer onto the stack of helpers used to map DNs between LDAP and SQL. |
| autocommit | - | Activates autocommit (default is off). |

---

## Configuration / Files Modified

- `/usr/local/etc/openldap/slapd.conf`

---

## Examples

- **Using an in-memory baseObject:**

```bash
database sql
baseObject extensibleObject
```

- **Using a specific file for the baseObject:**

```bash
database sql
baseObject my_base_object.ldif
```

---

## Exit Values

- 0: Success (General behavior)
- Non-zero: Error (General behavior)

---

## Caveats / Notes

- **Experimental Status:** This backend is experimental and may change in future releases.
- **Usage Limitation:** It is not designed as a general-purpose replacement for the standard MDB backend.
- **Filtering Restrictions:** The `hasSubordinates` operational attribute is only partially honored in filtering; specifically, `(!(hasSubordinates=TRUE))` will return no results instead of leaf entries.
- **Data Prettification:** DirectoryString values that are "prettified" (using underscores as spaces) are not currently honored if non-prettified data is written via the RDBMS.
- **Known Bug:** If the `ldap_entry_objclasses` table is empty, filters on the objectClass attribute will erroneously result in no candidates. A workaround is to add at least one row to that table.
