# slaptest Command Documentation

## Name and Synopsis

The `slaptest` command is used to check the conformance and sanity of the OpenLDAP `slapd` configuration. It parses either a configuration file or the `slapd-config` backend to ensure it adheres to general and backend-specific rules.

```bash
/usr/local/sbin/slaptest [-d debug-level] [-f slapd.conf] [-F confdir] [-ndbnum] [-o option[=value]] [-Q] [-u] [-v]
```

---

## Description

The primary purpose of `slaptest` is to verify that the configuration provided for the `slapd` daemon is valid before starting the service. It performs a sanity check by opening the specified `slapd.conf` file or the `slapd-config` backend and parsing it according to the rules defined by OpenLDAP.

If both the `-f` (file) and `-F` (directory) options are provided, `slaptest` will read the configuration from the file and automatically convert it into the directory format, writing the result to the specified directory. In dry-run mode (`-u`), the tool will verify the configuration syntax without attempting to open the underlying databases.

---

## Options Reference

The following table describes the available options for the `slaptest` command.

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -d | debug-level | Enables debugging messages at the specified level (refer to `slapd(8)` for details). |
| -f | slapd.conf | Specifies an alternative path to the `slapd.conf` file. |
| -F | confdir | Specifies a configuration directory. If used with `-f`, it triggers a conversion of the config file to directory format. |
| -n | dbnum | Tests only the specific database number listed in the configuration (e.g., `-n 0` for the config database). |
| -o | option\[=value\] | Specifies generic options such as `syslog`, `syslog-level`, or `syslog-user`. |
| -Q | | Quiet mode; only the exit code is returned (0 for success, non-zero for failure). |
| -u | | Enables dry-run mode. Configuration is checked without failing if databases cannot be opened. |
| -v | | Enables verbose output. |

---

## Configuration / Files Modified

* **Configuration File:** `/usr/local/etc/openldap/slapd.conf` (Default source).
* **Configuration Directory:** `/usr/local/etc/openldap/slapd.d` (Default source).
* **Modification Logic:** If `-f` and `-F` are used simultaneously, `slaptest` will create or overwrite files in the specified directory to convert the configuration format. This is bypassed if dry-run mode (`-u`) is active.

---

## Examples

* **Check a standard configuration file with verbose output:**

```bash
/usr/local/sbin/slaptest -f /usr/local/etc/openldap/slapd.conf -v
```

* **Test only the config database:**

```bash
/usr/local/sbin/slaptest -f /usr/local/etc/openldap/slapd.conf -n 0
```

* **Perform a dry-run check (syntax only):**

```bash
/usr/local/sbin/slaptest -f /usr/local/etc/openldap/slapd.conf -u
```

---

## Exit Values

* **0**: Success; the configuration is valid.
* **Non-zero**: Failure; one or more configuration errors were detected.

---

## Caveats / Notes

* **Conversion Behavior:** Be aware that using `-f` and `-F` together will actively modify files in the target directory unless dry-run mode is enabled.
* **Default Search Order:** If no options are specified, `slaptest` will first attempt to read the default configuration directory before falling back to the default configuration file.
* **Quiet Mode:** In `-Q` mode, all standard output is suppressed, making it suitable for use in automated scripts.
