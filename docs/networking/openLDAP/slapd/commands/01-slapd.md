# slapd Command Documentation

## Name and Synopsis

slapd is the stand-alone LDAP daemon that listens for LDAP connections on specified ports (defaulting to 389) and responds to received LDAP operations.

```bash
/usr/local/libexec/slapd  [-V[V[V]]  [-4|-6] [-T {acl|a[dd]|auth|c[at]|
d[n]|i[ndex]|m[odify]|p[asswd]|s[chema]|t[est]}]       [-d debug-level]
[-f slapd-config-file]       [-F slapd-config-directory]      [-h URLs]
[-n service-name]       [-s syslog-level]        [-l syslog-local-user]
[-o option[=value]] [-r directory] [-u user] [-g group] [-c cookie]
```

---

## Description

slapd functions as the primary daemon for providing LDAP services. It is typically invoked during system boot, usually via `/etc/rc.local`. Upon startup, the process normally forks and disassociates itself from the invoking terminal. If configured in the configuration file or directory, slapd will output its process ID to a `.pid` file and its command-line options to an `.args` file. Note that if the `-d` flag is provided (even with a zero argument), slapd will not fork or disassociate from the terminal.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -V\[V\[V\]\] | | Print version info. -VV exits after providing version; -VVV adds static overlays and backends. |
| -4 | | Listen on IPv4 addresses only. |
| -6 | | Listen on IPv6 addresses only. |
| -T tool | | Run in Tool mode (e.g., slapadd, slapcat, slapmodify). |
| -d debug-level | | Turn on debugging. If specified, slapd will not fork or disassociate from the terminal. |
| -s syslog-level | | Specify the debug-level for statements logged to syslog(8). |
| -n service-name | | Specify the service name for logging (default: "slapd"). |
| -l syslog-local-user | | Select the local user for syslog(8) (default: LOCAL4). |
| -f slapd-config-file | | Specify configuration file (default: /usr/local/etc/openldap/slapd.conf). |
| -F slapd-config-directory | | Specify config directory (default: /usr/local/etc/openldap/slapd.d). |
| -h URLlist | | Specify LDAP URLs to serve (e.g., ldap://, ldaps://, ldapi://). |
| -r directory | | Change current working directory and chroot(2) to the specified root directory. |
| -u user | | Run slapd with the specified user name or ID. |
| -g group | | Run slapd with the specified group name or ID. |
| -c cookie | | Provide a cookie for the syncrepl replication consumer. |
| -o option\[=value\] | | Generic options, such as slp support settings. |

---

## Configuration / Files Modified (if applicable)

* **slapd.conf**: Default configuration file (`/usr/local/etc/openldap/slapd.conf`).
* **slapd.d**: Default configuration directory (`/usr/local/etc/openldap/slapd.d`).
* **.pid**: File created to store the process ID.
* **.args**: File created to store command-line options.

---

## Examples

* Start slapd, fork it, and begin serving databases using the default config file:

```bash
/usr/local/libexec/slapd
```

* Start slapd with an alternate configuration file and verbose debugging:

```bash
/usr/local/libexec/slapd -f /var/tmp/slapd.conf -d 255
```

* Test the correctness of the configuration file:

```bash
/usr/local/libexec/slapd -Tt
```

---

## Exit Values

General behavior:

* **0**: Success.
* **Non-zero**: Indicates an error occurred during startup or execution.

---

## Caveats / Notes

* **Security Warning**: When enabling packet logging, the log files will contain bind passwords. Ensure that any redirected log files are properly read-protected.
* **IPC Socket Permissions**: The "x-mod" extension can be used to apply rough limitations on operations (read, write, execute) for specific listeners.
* **Chroot Behavior**: When used with the `-r` option, slapd will use the user/group database in a change root environment.
