# chown Command Documentation

## Name and Synopsis
The `chown` command is used to change the user and/or group ownership of files.

**Basic Usage:**
```bash
chown [OPTION]... [OWNER][:[GROUP]] FILE...
chown [OPTION]... --reference=RFILE FILE...
```

---

## Description
The `chown` command changes the user and/or group ownership of each given file.

*   If only an owner (a user name or numerical user ID) is provided, that user becomes the owner of each given file, while the files' group remains unchanged.
*   If the owner is followed by a colon and a group name (or numerical group ID), with no spaces between them, the group ownership of the files is changed as well.
*   If a colon but no group name follows the user name, that user is made the owner of the files and the group of the files is changed to that user's login group.
*   If the colon and group are given, but the owner is omitted, only the group of the files is changed (performing the same function as `chgrp`).
*   If only a colon is given, or if the entire operand is empty, neither the owner nor the group is changed.

---

## Options Reference
The following options are available for the `chown` command:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`-c`** | **`--changes`** | Like verbose, but report only when a change is made. |
| **`-f`** | **`--silent`, `--quiet`** | Suppress most error messages. |
| **`-v`** | **`--verbose`** | Output a diagnostic for every file processed. |
| **`--dereference`** | N/A | Affect the referent of each symbolic link (this is the default). |
| **`-h`** | **`--no-dereference`** | Affect symbolic links instead of any referenced file; useful only on systems that can change the ownership of a symlink. |
| **`--from=CURRENT_OWNER:CURRENT_GROUP`** | N/A | Change the ownership of each file only if its current owner and/or group match those specified here. Either may be omitted. |
| **`--no-preserve-root`** | N/A | Do not treat '/' specially (the default). |
| **`--preserve-root`** | N/A | Fail to operate recursively on '/'. |
| **`--reference=RFILE`** | N/A | Use RFILE's ownership rather than specifying values. RFILE is always dereferenced if a symbolic link. |
| **`-R`** | **`--recursive`** | Operate on files and directories recursively. |
| **`-H`** | N/A | If a command line argument is a symlink to a directory, traverse it. |
| **`-L`** | N/A | Traverse every symbolic link to a directory encountered. |
| **`-P`** | N/A | Do not traverse any symbolic links (this is the default). |
| **`--help`** | N/A | Display help and exit. |
| **`--version`** | N/A | Output version information and exit. |

---

## Examples
*   **Change owner only:**
    `chown root /u`
*   **Change owner and group:**
    `chown root:staff /u`
*   **Recursively change owner with symlink handling:**
    `chown -hR root /u`
