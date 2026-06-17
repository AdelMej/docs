# groupadd Command Documentation

## Name and Synopsis
The `groupadd` command is used to create a new group.

**Basic Usage:**
```bash
groupadd [OPTIONS] NEWGROUP
```

---

## Description
The `groupadd` command creates a new group account using the values specified on the command line plus the default values from the system. The new group is entered into the system files as needed.

**Group Name Constraints:**
*   Group names may contain lower and upper case letters, digits, underscores, or dashes.
*   They can end with a dollar sign ($).
*   Dashes are not allowed at the beginning of the groupname.
*   Fully numeric groupnames and groupnames `.` or `..` are disallowed.
*   Groupnames may only be up to 32 characters long.

---

## Options Reference
The following options are available for the `groupadd` command:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`-f`** | **`--force`** | Exit with success status if the specified group already exists. When used with `-g`, and the specified GID already exists, another unique GID is chosen (i.e., `-g` is turned off). |
| **`-g`** | **`--gid GID`** | The numerical value of the group's ID. Must be unique unless the `-o` option is used. Default is the smallest ID value greater than or equal to `GID_MIN` and greater than every other group. |
| **`-h`** | **`--help`** | Display help message and exit. |
| **`-K`** | **`--key KEY=VALUE`** | Overrides `/etc/login.defs` defaults (e.g., `GID_MIN`, `GID_MAX`). Multiple options can be specified. |
| **`-o`** | **`--non-unique`** | Permits the creation of a group with an already used numerical ID. |
| **`-p`** | **`--password PASSWORD`** | Defines an initial password for the group account (expected to be encrypted). Note: This is not recommended because the password will be visible in the process list. |
| **`-r`** | **`--system`** | Create a system group. Numeric identifiers are chosen from the `SYS_GID_MIN-SYS_GID_MAX` range defined in `login.defs`. |
| **`-R`** | **`--root CHROOT_DIR`** | Apply changes in the `CHROOT_DIR` directory and use configuration files from that directory. Only absolute paths are supported; no SELINUX support. |
| **`-P`** | **`--prefix PREFIX_DIR`** | Apply changes to configuration files under the root filesystem found under `PREFIX_DIR`. Intended for preparing a cross-compilation target. No SELINUX support. |
| **`-U`** | **`--users`** | A comma-separated list of usernames to add as members of the group. Default behavior depends on `USERGROUPS_ENAB` in `/etc/login.defs`. |

---

## Configuration
The following configuration variables in `/etc/login.defs` affect the behavior of this tool:
*   **`GID_MIN`**, **`GID_MAX`**: Range of group IDs used for the creation of regular groups (default 1000-60000).
*   **`SYS_GID_MIN`**, **`SYS_GID_MAX`**: Range of group IDs used for the creation of system groups (default 101 to `GID_MIN-1`).
*   **`MAX_MEMBERS_PER_GROUP`**: Maximum members per group entry. Default is 0 (no limits). Used to limit line length in the group file.

---

## Files Modified
*   `/etc/group`
*   `/etc/gshadow`
*   `/etc/login.defs`

---

## Exit Values
The `groupadd` command exits with the following values:
*   **0**: Success
*   **2**: Invalid command syntax
*   **3**: Invalid argument to option
*   **4**: GID is already used (when called without `-o`)
*   **9**: Group name is already used
*   **10**: Can't update group file

---

## Caveats
*   **External Databases:** You may not add a NIS or LDAP group; this must be performed on the corresponding server. If a groupname exists in an external database, `groupadd` will deny the creation request.
*   **Manual Cleanup:** You should manually check all file systems to ensure that no files remain owned by this group.
