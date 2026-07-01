# slapadd Command Documentation

## Name and Synopsis

`slapadd` is used to add entries specified in LDAP Directory Interchange Format (LDIF) to a `slapd` database.

```bash
/usr/local/sbin/slapadd [-b suffix] [-c] [-d debug-level] [-f slapd.conf] [-F confdir] [-g] [-j lineno] [-l ldif-file] [-n dbnum] [-o option[=value]] [-q] [-s] [-S SID] [-u] [-v] [-w]
```

---

## Description

The `slapadd` utility imports LDIF data into a specific `slapd` database. The target database is determined either by a provided suffix or by a specific database index number. When entries are added, the tool also updates any subordinate databases unless the `-g` option is used to restrict processing to only the specified database.

It is important to note that all files created by `slapadd` belong to the user account running the command; users should ensure they run the command with the same identity as the `slapd` daemon or update file ownership after completion. Furthermore, `slapadd` will automatically perform relevant indexing while adding data if indices are configured in the database.

---

## Options Reference

The following table lists the options available for the `slapadd` command.

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -b | suffix | Use the specified suffix to determine which database to add entries to. |
| -c | | Enable continue mode (ignore errors). |
| -d | debug-level | Enable debugging messages as defined by the specified level. |
| -f | slapd.conf | Specify an alternative `slapd.conf` file. |
| -F | confdir | Specify a config directory. If both `-f` and `-F` are used, the file is converted to directory format and written to the target. |
| -g | | Disable subordinate gluing; only the specified database will be processed. |
| -j | lineno | Jump to the specified line number in the LDIF file before processing. |
| -l | ldif-file | Read LDIF from the specified file instead of standard input. |
| -n | dbnum | Add entries to the dbnum-th database listed in the configuration (e.g., use `-n 0` for config). |
| -o | option\[=value\] | Specify generic options (e.g., `syslog`, `schema-check`, `value-check`). |
| -q | | Enable quick mode (fewer integrity checks). Improves load time but can leave the database unusable if interrupted. |
| -s | | Disable schema checking. |
| -S | SID | Server ID to use in generated entryCSN and contextCSN. Defaults to 0. |
| -u | | Enable dry-run mode (do not write to the backend). |
| -v | | Enable verbose mode. |
| -w | | (Over)write syncrepl context information after all entries are added. |

---

## Configuration / Files Modified

* **LDIF Input:** Data is read from standard input or a specified file via `-l`.
* **Subordinate Databases:** Updated automatically unless `-g` is specified.
* **File Ownership:** Created files belong to the identity of the user running `slapadd`.

---

## Examples

* **Import entries from a specific LDIF file:**

```bash
/usr/local/sbin/slapadd -l ldif
```

---

## Exit Values

* **0**: Success.
* **Non-zero**: Failure; indicates an error occurred during the addition of entries or processing of the LDIF (General behavior).

---

## Caveats / Notes

* **Database Consistency:** The `slapd` service should not be running while executing `slapadd` to ensure database consistency.
* **Existing Directories:** It is recommended to use `ldapadd(1)` when adding new entries into an existing directory, as `slapadd` may not provide sufficient naming or schema checks.
* **Quick Mode Warning:** Using the `-q` option skips consistency checks during the write process; any interruption or error during this mode may result in an unusable database.
