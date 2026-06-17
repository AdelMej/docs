# lsattr Command Documentation

## Name and Synopsis

* **Description:** `lsattr` is used to list the attributes of files and directories on ext2, ext3, and ext4 file systems.

* **Basic Usage:**

```bash
lsattr [ -RVadlpv ] [ files... ]
```

---

## Description

The primary purpose of the `lsattr` command is to display the specific filesystem attributes associated with files and directories. It allows users to verify the state of various file flags, such as those that control immutability or append-only status. The command reads the attributes directly from the filesystem metadata of ext2, ext3, and ext4 partitions.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -R | N/A | Recursively list attributes of directories and their contents. |
| -V | N/A | Display the program version. |
| -a | N/A | List all files in directories, including those starting with `.`. |
| -d | N/A | List directories like other files, rather than listing their contents. |
| -l | N/A | Print the options using long names instead of single character abbreviations. |
| -p | N/A | List the file's project number. |
| -v | N/A | List the file's version/generation number. |

---

## Examples

* **List attributes of a specific file:**

```bash
lsattr filename
```

* **Recursively list attributes in a directory:**

```bash
lsattr -R /path/to/directory
```

* **List attributes of all files (including hidden files):**

```bash
lsattr -a
```

* **List directories as individual entries without showing contents:**

```bash
lsattr -d /path/to/directory
```

---

## Exit Values

* 0: Success. [General behavior]
* Non-zero: Error (e.g., file not found, permission denied, or unsupported filesystem). [General behavior]

---

## Caveats / Notes

* **Filesystem Limitation:** `lsattr` is specifically designed for ext2, ext3, and ext4 file systems.
* **Attribute Definitions:** For a detailed description of what the specific attributes mean, refer to the `chattr(1)` manual page.
