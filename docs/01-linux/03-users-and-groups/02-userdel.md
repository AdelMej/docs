# userdel Command Documentation

## Name and Synopsis

The `userdel` command is used to delete a user account and its related files.

**Basic Usage:**

```bash
userdel [options] LOGIN
```

---

## Description

The `userdel` command modifies the system account files by deleting all entries that refer to the specific user name `LOGIN`. The named user must exist for this command to be executed.

---

## Options Reference

The following options are available for the `userdel` command:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`-f`** | **`--force`** | Forces the removal of the user account and any other requested actions, skipping safety checks. This is dangerous and may leave the system in an inconsistent state. |
| **`-h`** | **`--help`** | Displays the help message and exits. |
| **`-r`** | **`--remove`** | Removes files in the user's home directory (including the directory itself) and the user's mail spool. Files on other file systems must be deleted manually. |
| **`-R`** | **`--root CHROOT_DIR`** | Applies changes within the specified `CHROOT_DIR` and uses configuration files from that directory. |
| **`-P`** | **`--prefix PREFIX_DIR`** | Applies changes within the directory tree starting with `PREFIX_DIR`. Intended for cross-compilation targets. |
| **`-Z`** | **`--selinux-user`** | Removes any SELinux user mapping for the user's login. |

---

## Configuration

Several configuration variables in `/etc/login.defs` affect `userdel`:

* **`MAIL_DIR`** and **`MAIL_FILE`**: Used to manage the user's mail spool [12].
* **`USERDEL_CMD`**: If defined, this script is run when removing a user to handle additional tasks like removing cron or at jobs.
* **`USERGROUPS_ENAB`**: If set to "yes", `userdel` will remove the user's group if it contains no more members.

---

## Files Modified

The command modifies the following system files:

* `/etc/group`
* `/etc/login.defs`
* `/etc/passwd`
* `/etc/shadow`
* `/etc/shadow-maint/userdel-pre.d/*` and `userdel-post.d/*`
* `/etc/subgid`
* `/etc/subuid`

---

## Exit Statuses

The `userdel` command exits with the following values:

* **0**: Success
* **1**: Can't update password file
* **2**: Invalid command syntax
* **6**: Specified user doesn't exist
* **8**: User currently logged in
* **10**: Can't update group file
* **12**: Can't remove home directory

---

## Caveats

* **Running Processes:** `userdel` will not allow the removal of an account if there are active processes belonging to that account. You may need to kill those processes or use the `-f` option to force deletion.
* **Manual Cleanup:** You should manually check all file systems to ensure no files remain owned by the deleted user.
* **NIS Constraints:** You cannot remove NIS attributes on a NIS client; this must be performed on the NIS server.
