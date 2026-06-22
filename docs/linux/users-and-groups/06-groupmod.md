# groupmod Command Documentation

## Name and Synopsis

The `groupmod` command is used to modify a group definition on the system.

**Basic Usage:**

```bash
groupmod [options] GROUP
```

---

## Description

The `groupmod` command modifies the definition of the specified `GROUP` by modifying the appropriate entry in the group database.

---

## Options Reference

The following options are available for the `groupmod` command:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`-a`** | **`--append`** | If group members are specified with `-U`, append them to the existing member list, rather than replacing it. |
| **`-g`** | **`--gid GID`** | The group ID of the given `GROUP` will be changed to `GID`. The value must be a non-negative decimal integer and unique unless the `-o` option is used. Users who use the group as their primary group will be updated automatically; however, any files with the old group ID must have their group ID changed manually. |
| **`-h`** | **`--help`** | Display help message and exit. |
| **`-n`** | **`--new-name NEW_GROUP`** | The name of the group will be changed from `GROUP` to `NEW_GROUP`. |
| **`-o`** | **`--non-unique`** | When used with the `-g` option, allow changing the group GID to a non-unique value. |
| **`-p`** | **`--password PASSWORD`** | Defines an initial encrypted password for the group account (not recommended as it is visible in the process list). |
| **`-R`** | **`--root CHROOT_DIR`** | Apply changes in the `CHROOT_DIR` directory and use the configuration files from that directory. Only absolute paths are supported. No SELINUX support. |
| **`-P`** | **`--prefix PREFIX_DIR`** | Apply changes within the directory tree starting with `PREFIX_DIR` and use the configuration files located there. Does not chroot; NIS and LDAP users/groups are not verified. No SELINUX support. |
| **`-U`** | **`--users`** | A comma-separated list of usernames to add as members of the group. If `-g` and `-U` are not specified, default behavior depends on `USERGROUPS_ENAB` in `/etc/login.defs`. |

---

## Configuration

The following configuration variable in `/etc/login.defs` affects the behavior of this tool:

* **`MAX_MEMBERS_PER_GROUP`**: Maximum members per group entry. When the maximum is reached, a new group entry (line) is started in `/etc/group` (with the same name, same password, and same GID). The default value is 0, meaning that there are no limits in the number of members in a group.

---

## Files Modified

* `/etc/group`
* `/etc/gshadow`
* `/etc/login.defs`
* `/etc/passwd`

---

## Exit Values

The `groupmod` command exits with the following values:

* **0**: Success
* **2**: Invalid command syntax
* **3**: Invalid argument to option
* **4**: Group ID already in use
* **6**: Specified group doesn't exist
* **9**: Group name already in use
* **10**: Can't update group file
* **11**: Can't setup cleanup service
* **12**: Can't determine your username for use with pam
* **13**: PAM returned an error
