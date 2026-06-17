# nft Command Documentation

## Name and Synopsis

The `nft` command is the administration tool for the nftables framework, used to set up, maintain, and inspect packet filtering and classification rules within the Linux kernel's `nf_tables` subsystem.

**Basic Usage:**
```bash
nft [ -nNscaeSupyjtT ] [ -I directory ] [ -f filename | -i | cmd ... ]
```

---

## Description

The `nft` command-line tool is used to manage rulesets in the Linux kernel's nftables framework. It interacts with the `nf_tables` subsystem (where "nf" stands for Netfilter) to handle packet filtering and classification. The tool allows users to create tables, chains, rules, sets, maps, and other stateful objects to control network traffic. It supports various input methods, including files, interactive shells, and direct commands.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -h | --help | Show help message and all options. |
| -v | --version | Show version. |
| -V | | Show long version information, including compile-time configuration. |
| -f | --file filename | Read input from filename. If filename is -, read from stdin. |
| -D | --define name=value | Define a variable (only usable with -f). |
| -i | --interactive | Read input from an interactive readline CLI. |
| -I | --includepath directory | Add directory to the list of directories to be searched for included files. |
| -c | --check | Check commands validity without actually applying changes. |
| -o | --optimize | Optimize your ruleset (can be combined with -c). |
| -a | --handle | Show object handles in output. |
| -s | --stateless | Omit stateful information of rules and stateful objects. |
| -t | --terse | Omit contents of sets from output. |
| -S | --service | Translate ports to service names as defined by /etc/services. |
| -N | --reversedns | Translate IP address to names via reverse DNS lookup. |
| -u | --guid | Translate numeric UID/GID to names (from /etc/passwd and /etc/group). |
| -n | --numeric | Print fully numerical output. |
| -y | --numeric-priority | Display base chain priority numerically. |
| -p | --numeric-protocol | Display layer 4 protocol numerically. |
| -T | --numeric-time | Show time, day and hour values in numeric format. |
| -e | --echo | Print notifications when inserting items via add, insert, or replace. |
| -j | --json | Format output in JSON. |
| -d | --debug level | Enable debugging (scanner, parser, eval, netlink, mnl, proto-ctx, segtree, all). |

---

## Configuration / Files Modified

The following system files are used by `nft` for data lookups and translations:
* `/etc/services`: Used by the `-S` option to translate ports to service names.
* `/etc/passwd`: Used by the `-u` option to translate numeric UIDs to names.
* `/etc/group`: Used by the `-u` option to translate numeric GIDs to names.

---

## Examples

* **Interactive Mode:** Start an interactive shell for rule management.
```bash
nft --interactive
```

* **Creating a Table and Chain:** Create a new table in the `inet` family with an input chain.
```bash
create table inet mytable
add chain inet mytable myin { type filter hook input priority filter; }
```

* **Adding Rules and Counters:** Add a counter to a specific chain.
```bash
add rule inet mytable myin counter
```

* **Using Symbolic Variables:** Define variables for interface names.
```bash
define int_if1 = eth0
define int_if2 = eth1
define int_ifs = { $int_if1, $int_if2 }
filter input iif $int_ifs accept
```

* **JSON Output:** View the ruleset in JSON format.
```bash
nft -j list ruleset
```

---

## Exit Values

* **0**: Success.
* **1**: Unspecified errors.
* **2**: Memory allocation errors.
* **3**: Unable to open Netlink socket.

---

## Caveats / Notes

* **Rule Locations:** Rule locations are internally identified by handles. While indexes can be used, they are translated in userspace; if a concurrent ruleset change occurs during translation, the effective rule index might change.
* **Wildcards:** Wildcard matching on interface names is supported (e.g., `eth*`). However, nftables does not accept interface names consisting solely of a wildcard character.
* **Handle vs Index:** If a referred rule was deleted before a command is executed, the command will be rejected by the kernel as if an invalid handle were provided.
* **Included Files:** Files beginning with a dot (.) are not matched by `include` statements.
