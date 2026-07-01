# slapcat Command Documentation

## Name and Synopsis

The `slapcat` utility is used to generate LDAP Directory Interchange Format (LDIF) output based on the contents of a `slapd` database.

```bash
/usr/local/sbin/slapcat [-afilter] [-bsuffix] [-c] [-ddebug-level] [-fslapd.conf] [-Fconfdir] [-g] [-HURI] [-lldif-file] [-ndbnum] [-ooption[=value]] [-ssubtree-dn] [-v]
```

---

## Description

`slapcat` opens a specified database, determined by a database number or suffix, and writes the corresponding LDIF data to standard output or a designated file. The tool presents entry records in database order rather than superior first order. The output includes all user and operational attributes stored in the database but excludes dynamically generated attributes (such as `subschemaSubentry`).

The resulting LDIF output is primarily intended for use as input for `slapadd(8)`. It generally cannot be used as input for `ldapadd(1)` or other LDAP clients without manual editing to reorder records into superior first order and remove no-user-modification operational attributes.

---

## Options Reference

The following table lists the options available for the `slapcat` command.

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -a | filter | Only dump entries matching the asserted filter. (Deprecated; use `-H`). |
| -b | suffix | Use the specified suffix to determine which database to generate output for. |
| -c | | Enable continue (ignore errors) mode. Multiple occurrences make the tool try harder. |
| -d | debug-level | Enable debugging messages as defined by the specified debug level. |
| -f | slapd.conf | Specify an alternative `slapd.conf` file. |
| -F | confdir | Specify a config directory. If both `-f` and `-F` are used, the file is converted to directory format. |
| -g | | Disable subordinate gluing; only the specified database will be processed. |
| -H | URI | Use DN, scope, and filter from URI to handle only matching entries. |
| -l | ldif-file | Write LDIF to the specified file instead of standard output. |
| -n | dbnum | Generate output for the dbnum-th database listed in the configuration. |
| -o | option\[=value\] | Specify options like `syslog`, `syslog-level`, `syslog-user`, or `ldif_wrap`. |
| -s | subtree-dn | Only dump entries in the subtree specified by this DN (Deprecated; use `-H`). |
| -v | | Enable verbose mode. |

---

## Configuration / Files Modified

* **Database Source:** Determined by the configuration file (`slapd.conf`), configuration directory, or database number/suffix flags.
* **Output Destination:** Standard output (default) or a file specified via the `-l` option.

---

## Examples

* **Make a text backup of the SLAPD database into a file named `ldif`:**

```bash
/usr/local/sbin/slapcat -l ldif
```

* **Dump entries matching a specific filter:**

```bash
/usr/local/sbin/slapcat -H ldap:///???(filter)
```

---

## Exit Values

* **0**: Success.
* **Non-zero**: Failure; indicates an error occurred during database processing or LDIF generation (General behavior).

---

## Caveats / Notes

* **Database Consistency:** For some backend types, `slapd` should not be running (at least not in read-write mode) when executing `slapcat` to ensure database consistency.
* **Safe Backends:** It is always safe to run `slapcat` with the `slapd-mdb` and `slapd-null` backends.
* **Tool Compatibility:** The output of `slapcat` is designed for `slapadd`. If you need to use the output with `ldapadd`, you must manually edit the records to reorder them into superior first order and remove operational attributes that are not allowed for user modification.
