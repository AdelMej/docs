# Scripting

Many people like to maintain their ruleset in shell scripts, which allows them to add comments and arrange rules in a more human-friendly way. However, this is problematic because shell scripts break atomicity when applying the ruleset, causing the filtering policy to be applied inconsistently during the loading time.

Fortunately, nftables provides a native scripting environment to address these concerns. This environment allows you to include other ruleset files, define variables, and add comments. To restore the content of this native script, use the command:

```bash
nft -f my-ruleset.file
```

To create an nftables script, you must add the following header to your script file:

```bash
#!/usr/sbin/nft -f
```

## 1 Adding comments

You can add comments to your file using the `#` character. Everything following the `#` will be ignored.

```bash
#!/usr/sbin/nft -f

#
# table declaration
#
add table filter

#
# chain declaration
#
add chain filter input { type filter hook input priority 0; policy drop; }

#
# rule declaration
#
add rule filter input ct state established,related counter accept
```

## 2 Including files

Other files can be included using the `include` statement. The directories searched for include files can be specified using the `-I/--includepath` option. You can override this behavior by:

* Prepending `./` to your path to force inclusion of files in the current working directory (relative path).
* Using `/` for file locations expressed as an absolute path.

If `-I/--includepath` is not specified, nft relies on the default directory specified at compile time (retrievable via the `-h/--help` option).

Include statements support standard shell wildcard symbols (`*`, `?`, `[]`). Having no matches for an include statement is not an error if wildcard symbols are used. Wildcard matches are loaded in alphabetical order.

**Note:** Files beginning with a dot (`.`) are not matched by include statements.

```bash
#!/usr/sbin/nft -f

# include a single file using the default search path
include "ipv4-nat.ruleset"

# include all files ending in *.nft in the default search path
include "*.nft"

# include all files in a given directory using an absolute path
include "/etc/nftables/*"
```

## 3 Defining variables

You can use the `define` keyword to define variables. For example, here is a simple ruleset to account for traffic from 8.8.8.8:

```bash
#!/usr/sbin/nft -f

define google_dns = 8.8.8.8

add table filter
add chain filter input { type filter hook input priority 0; }
add rule filter input ip saddr $google_dns counter
```

You can also define a variable for sets:

```bash
#!/usr/sbin/nft -f

define ntp_servers = { 84.77.40.132, 176.31.53.99, 81.19.96.148, 138.100.62.8 }

add table filter
add chain filter input { type filter hook input priority 0; }
add rule filter input ip saddr $ntp_servers counter
```

**Note:** Brackets have special semantics when used from rules as they indicate the variable represents a set. To avoid overkill, do not use brackets for single elements (e.g., `define google_dns = { 8.8.8.8 }`); instead, use the singleton definition: `define google_dns = 8.8.8.8`.

## 4 File formats

The `nft -f <filename>` command accepts two formats:

1. The format seen in the output of `nft list table`.
2. The syntax of calling the nft binary several times in an atomic fashion.

**Example of nftables output format:**

```bash
#!/usr/sbin/nft -f

define ntp_servers = { 84.77.40.132, 176.31.53.99, 81.19.96.148, 138.100.62.8 }

#flush table nat
table ip nat {
 chain prerouting {
  type filter hook prerouting priority 0; policy accept;
                ip saddr $ntp_servers counter
 }

 chain postrouting {
  type filter hook postrouting priority 100; policy accept;
 }
}
```

**Example of scripted config format:**

```bash
#!/usr/sbin/nft -f

define ntp_servers = { 84.77.40.132, 176.31.53.99, 81.19.96.148, 138.100.62.8 }

add table filter
add chain filter input { type filter hook input priority 0; }
add rule filter input ip saddr $ntp_servers counter
```

You can freely translate between these two formats as the syntax is mostly the same, with only the organization differing.

## 5 Building an nft file from scripts

Although not necessarily recommended, you can use your choice of scripting language to build a single text file in a format accepted by nft, then load it atomically with `nft -f <your_file_in_nft_format>`. This is useful when coordinating nft configuration with other subsystems using different file formats.

## 6 Using nftables from Python

The same functionality provided by the `nft` command-line utility is available within Python programs via the high-level library `libnftables`.
