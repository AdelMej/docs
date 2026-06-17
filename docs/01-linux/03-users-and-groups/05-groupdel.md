# groupdel Command Documentation

## Name and Synopsis

The `groupdel` command is used to delete a group.

**Basic Usage:**

```bash
groupdel [options] GROUP
```

---

## Description

The `groupdel` command modifies the system account files, deleting all entries that refer to the specified `GROUP`. The named group must exist for this command to be executed.

---

## Options Reference

The following options are available for the `groupdel` command:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`-f`** | **`--force`** | Forces the removal of the group, even if there is a user having the group as their primary one. |
| **`-h`** | **`--help`** | Displays the help message and exits. |
| **`-R`** | **`--root CHROOT_DIR`** | Applies changes in the `CHROOT_DIR` directory and uses configuration files from that directory. Only absolute paths are supported; no SELINUX support. |
| **`-P`** | **`--prefix PREFIX_DIR`** | Applies changes in the `PREFIX_DIR` directory and uses configuration files from that directory. This option does not chroot and is intended for preparing a cross-compilation target. Some limitations: NIS and LDAP users/groups are not verified. PAM authentication is using the host files. No SELINUX support. |

---

## Configuration

The following configuration variable in `/etc/login.defs` affects the behavior of this tool:

* **`MAX_MEMBERS_PER_GROUP`**: Maximum members per group entry. When the maximum is reached, a new group entry (line) is started in `/etc/group` (with the same name, same password, and same GID). The default value is 0, meaning that there are no limits in the number of members in a group.

---

## Files Modified

* `/etc/group`
* `/etc/gshadow`

---

## Exit Values

The `groupdel` command exits with the following values:

* **0**: Success
* **2**: Invalid command syntax
* **6**: Specified group doesn't exist
* **8**: Can't remove user's primary group
* **10**: Can't update group file

---

## Caveats

* **Primary Group Restriction:** You may not remove the primary group of any existing user. You must remove the user before you can remove the group.
* **Manual Cleanup:** You should manually check all file systems to ensure that no files remain owned by this group .
