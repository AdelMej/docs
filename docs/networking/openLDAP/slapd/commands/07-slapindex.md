# slapindex Command Documentation

## Name and Synopsis

`slapindex` is a utility used to regenerate indices for a SLAPD database based on its current content.

```bash
/usr/local/sbin/slapindex [-b suffix] [-c] [-d debug-level] [-f slapd.conf] [-F confdir] [-g] [-n dbnum] [-o option[=value]] [-q] [-t] [-v] [attr[...]]
```

---

## Description

The primary purpose of `slapindex` is to rebuild the indices of a SLAPD database. When executed, it opens the database—selected by either a suffix or a database number—and updates the indices for all values of all attributes across all entries. If a specific list of attributes is provided as arguments, only those attributes will be re-indexed. By default, databases configured as subordinates are also re-indexed unless the `-g` option is used.

A critical operational detail is that all files created by `slapindex` inherit the identity of the user running the command. Therefore, it should be run with the same identity as the `slapd(8)` process or file ownership must be corrected manually afterward.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -b suffix | | Use the specified suffix to determine which database to generate output for. Cannot be used with `-n`. |
| -c | | Enable continue mode (ignores errors during processing). |
| -d debug-level | | Enable debugging messages at the specified level. |
| -f slapd.conf | | Specify an alternative `slapd.conf(5)` file. |
| -F confdir | | Specify a configuration directory. If both `-f` and `-F` are used, the config file is converted to directory format. |
| -g | | Disable subordinate gluing; only the specified database will be processed. |
| -n dbnum | | Generate output for the dbnum-th database in the config file (e.g., `-n 0` for `slapd-config(5)`). Cannot be used with `-b`. |
| -o option:\[=value\] | | Specify a generic option or value (e.g., `syslog`, `syslog-level`, `syslog-user`). |
| -q | | Enable quick mode. Performs fewer integrity checks to improve speed, but risks database usability if interrupted. |
| -t | | Enable truncate mode. Empties an index database before indexing. Only applicable for back-mdb. |
| -v | | Enable verbose mode. |

---

## Configuration / Files Modified

* **Configuration:** The command reads `slapd.conf(5)` or a configuration directory defined by `-f` or `-F`.
* **Files Modified:** This command creates and updates index files within the database directory. These files are owned by the user executing the command.

---

## Examples

* **Reindex the entire SLAPD database:**

```bash
/usr/local/sbin/slapindex
```

* **Regenerate the index for a specific attribute (e.g., "uid"):**

```bash
/usr/local/sbin/slapindex uid
```

---

## Exit Values

* **0:** The operation completed successfully.
* **Non-zero:** An error occurred during the indexing process.

---

## Caveats / Notes

* **Consistency Warning:** `slapd(8)` should not be running (at least not in read-write mode) while `slapindex` is being executed to ensure database consistency.
* **File Ownership:** Ensure that the user running `slapindex` has the correct permissions and that the resulting files are owned by the same identity as the `slapd(8)` process.
