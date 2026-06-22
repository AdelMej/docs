# chgrp Command Documentation

## Name and Synopsis

The `chgrp` command is used to change group ownership.

**Basic Usage:**

```bash
chgrp [OPTION]... GROUP FILE...
chgrp [OPTION]... --reference=RFILE FILE...
```

---

## Description

The `chgrp` command changes the group of each file to a specified group. With the `--reference` option, the group of each file is changed to that of a reference file (RFILE).

---

## Options Reference

The following options are available for the `chgrp` command:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`-c`** | **`--changes`** | Like verbose but report only when a change is made. |
| **`-f`** | **`--silent`, `--quiet`** | Suppress most error messages. |
| **`-v`** | **`--verbose`** | Output a diagnostic for every file processed. |
| **`--dereference`** | N/A | Affect the referent of each symbolic link (this is the default), rather than the symbolic link itself. |
| **`-h`** | **`--no-dereference`** | Affect symbolic links instead of any referenced file; useful only on systems that can change the ownership of a symlink. |
| **`--from=CURRENT_OWNER:CURRENT_GROUP`** | N/A | Change the ownership of each file only if its current owner and/or group match those specified here. Either may be omitted, in which case a match is not required for the omitted attribute. |
| **`--no-preserve-root`** | N/A | Do not treat '/' specially (the default). |
| **`--preserve-root`** | N/A | Fail to operate recursively on '/'. |
| **`--reference=RFILE`** | N/A | Use RFILE's ownership rather than specifying values. RFILE is always dereferenced if a symbolic link. |
| **`-R`** | **`--recursive`** | Operate on files and directories recursively. |
| **`-H`** | N/A | If a command line argument is a symlink to a directory, traverse it. |
| **`-L`** | N/A | Traverse every symbolic link to a directory encountered. |
| **`-P`** | N/A | Do not traverse any symbolic links. |
| **`--help`** | N/A | Display this help and exit. |
| **`--version`** | N/A | Output version information and exit. |

---

## Examples

* **Change group only:**
    `chgrp staff /u`
* **Recursively change group with symlink handling:**
    `chgrp -hR staff /u`
