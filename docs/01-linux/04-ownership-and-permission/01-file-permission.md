# Linux File Permissions Documentation

Linux file permissions are essential for system security, defining who can access files and directories and what actions they can perform [1].

### Viewing and Reading Permissions

To view metadata, including permissions, owners, and groups, use the command:
`$ ls -l`
The output shows a string like `rw-r--r--`, which represents three sets of permissions:

1. **Owner:** The first set (e.g., `rw-`) applies to the user who owns the file.
2. **Group:** The second set (e.g., `r--`) applies to members of the group that owns the file.
3. **Others:** The third set (e.g., `r--`) applies to all other users.

### Permission Types

* **Read (r):** Allows viewing file contents or listing directory files.
* **Write (w):** Allows modifying file contents or adding/removing files in a directory.
* **Execute (x):** Allows running binaries/scripts or entering a directory (`cd`).

### Permission Modes

Permissions can be represented in two modes:

* **Symbolic Mode:** Uses letters (`u` for owner, `g` for group, `o` for others; `r`, `w`, `x` for permissions).
* **Numeric (Octal) Mode:** A three-digit value where each digit represents a user class. Each permission has a specific value: **r=4, w=2, x=1**.

* `r` → Read (4)
* `w` → Write (2)
* `x` → Execute (1)
* `-` → No permission

**Examples:**

* `rwx` = 7
* `rw-` = 6
* `r--` = 4

### Management and Special Permissions

* **chmod:** Changes permissions using symbolic or numeric modes.
* **chown/chgrp:** Changes file ownership or group membership.
* **Special Permissions:**
  * **SUID:** Executes the file as the owner.
  * **SGID:** Executes the file as the group owner; files created in a directory inherit the directory's group.
  * **Sticky Bit:** Restricts deletion so only the file owner can remove it from a directory.

### Defaults and Common Sets

**Default Permissions (umask)**

* `umask`: Displays the current mask used to determine default permissions.
* `umask 022`: Typically results in default permissions of `755` for directories and `644` for files.

**Common Permission Sets**

* `755`: Standard for executables (Owner: rwx, Group/Others: r-x).
* `644`: Standard for public files (Owner: rw-, Group/Others: r--).
* `700`: For private files (Owner: rwx, Group/Others: ---).
* `777`: **Warning:** Provides full access to everyone; generally should be avoided.
