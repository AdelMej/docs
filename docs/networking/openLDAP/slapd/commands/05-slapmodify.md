# slapmodify Command Documentation

## Name and Synopsis

slapmodify is used to apply modifications specified in LDAP Directory Interchange Format (LDIF) to a slapd(8) database.

```bash
/usr/local/sbin/slapmodify [-b suffix] [-c] [-d debug-level] [-f slapd.conf] [-F confdir] [-g] [-j lineno] [-l ldif-file] [-n dbnum] [-o option[=value]] [-q] [-s] [-S SID] [-u] [-v] [-w]
```

---

## Description

The primary purpose of `slapmodify` is to perform modifications on an LDAP database using LDIF input read from standard input or a specified file. The command determines which database to modify based on the provided suffix or database number. By default, databases configured as subordinates of the target database are also updated unless the `-g` option is used to disable subordinate gluing.

The utility performs relevant indexing while modifying the database if any are configured. It is important to note that all files created by `slapmodify` will belong to the identity of the user executing the command; therefore, it should ideally be run with the same identity as the `slapd(8)` process.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -b | suffix | Use the specified suffix to determine which database to add entries to. Cannot be used with -n. |
| -c | | Enable continue mode (ignore errors). |
| -d | debug-level | Enable debugging messages as defined by the specified debug level. |
| -f | slapd.conf | Specify an alternative slapd.conf(5) file. |
| -F | confdir | Specify a configuration directory. |
| -g | | Disable subordinate gluing; only the specified database will be processed. |
| -j | lineno | Jump to the specified line number in the LDIF file before processing. |
| -l | ldif-file | Read LDIF from the specified file instead of standard input. |
| -n | dbnum | Perform changes on the dbnum-th database listed in the configuration file. |
| -o | option\[=value\] | Specify options such as syslog, schema-check, or value-check. |
| -q | | Enable quick mode (fewer integrity checks). Results in an unusable database if errors occur. |
| -s | | Disable schema checking. |
| -S | SID | Server ID to use in generated entryCSN and contextCSN (if -w is set). |
| -u | | Enable dry-run mode (do not write to backend). |
| -v | | Enable verbose mode. |
| -w | | Write syncrepl context information; updates contextCSN with the greatest CSN in the database. |

---

## Configuration / Files Modified (if applicable)

* **slapd.conf**: The main configuration file used to identify databases and options.
* **LDIF files**: Input files containing the modification data.
* **Database files**: The physical database files are modified directly based on the configuration provided.

---

## Examples

* Apply modifications from a specific LDIF file:

```bash
/usr/local/sbin/slapmodify -l ldif
```

---

## Exit Values

General behavior:

* **0**: Success.
* **Non-zero**: Indicates an error occurred during processing (specific codes may vary by implementation).

---

## Caveats / Notes

* **Consistency Warning**: The `slapd(8)` service should not be running when using `slapmodify` to ensure database consistency.
* **Backend Limitations**: Not all backends support all types of modification; specifically, `modrdn changetype` is not implemented for current backends.
* **Schema Checks**: `slapmodify` may not provide naming or schema checks. It is advisable to use `ldapmodify(1)` whenever possible for these requirements.
