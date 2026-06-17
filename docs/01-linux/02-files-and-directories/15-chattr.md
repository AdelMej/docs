# chattr Command Documentation

## Name and Synopsis

* **Description:** `chattr` is used to change file attributes on a Linux file system.

* **Basic Usage:**

```bash
chattr [ -RVf ] [ -v version ] [ -p project ] [ mode ] files...
```

---

## Description

The `chattr` command provides a mechanism to modify specific filesystem-level flags for files and directories. It utilizes a symbolic mode format where the operator `+` adds an attribute, `-` removes an attribute, and `=` sets only the specified attributes for the file.

The letters used to select attributes include: append only (`a`), no atime updates (`A`), compressed (`c`), no copy on write (`C`), no dump (`d`), synchronous directory updates (`D`), extent format (`e`), case-insensitive directory lookups (`F`), immutable (`i`), data journaling (`j`), don't compress (`m`), project hierarchy (`P`), secure deletion (`s`), synchronous updates (`S`), no tail-merging (`t`), top of directory hierarchy (`T`), undeletable (`u`), and direct access for files (`x`).

Note that certain attributes are read-only and can be listed using `lsattr` but cannot be modified by `chattr`: encrypted (`E`), indexed directory (`I`), inline data (`N`), and verity (`V`).

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -R | N/A | Recursively change attributes of directories and their contents. |
| -V | N/A | Be verbose with output and display the program version. |
| -f | N/A | Suppress most error messages. |
| -v | version | Set the file's version/generation number. |
| -p | project | Set the file's project number. |

---

## Configuration / Files Modified

This command modifies filesystem metadata attributes. It does not modify system configuration files (such as those in `/etc/`).

---

## Examples

* **Make a file immutable:**

```bash
chattr +i filename
```

* **Remove the append-only attribute from a file:**

```bash
chattr -a filename
```

* **Set specific attributes (e.g., no atime updates and immutable):**

```bash
chattr =Ai filename
```

* **Recursively set the immutable attribute on a directory and all its contents:**

```bash
chattr -R +i /path/to/directory
```

---

## Exit Values

* 0: Success. [General behavior]
* Non-zero: Error (e.g., permission denied, invalid mode, or unsupported attribute). [General behavior]

---

## Caveats / Notes

* **Filesystem Support:** Not all flags are supported by all filesystems. Refer to specific filesystem manual pages (e.g., btrfs, xfs, ext4) for details.
* **Kernel Limitations:** The `c`, `s`, and `u` attributes are not honored by the current mainline Linux kernels for ext2, ext3, and ext4 filesystems.
* **Permissions:** Setting or clearing the `a` and `i` attributes requires superuser privileges or the `CAP_LINUX_IMMUTABLE` capability.
* **Read-only Attributes:** The `E`, `I`, `N`, and `V` attributes are read-only and cannot be changed via this command.
