# usermod Command Documentation

## Name and Synopsis

The `usermod` command is used to modify a user account.

**Basic Usage:**

```bash
usermod [options] LOGIN
```

---

## Description

The `usermod` command modifies the system account files for the specified user.

---

## Options Reference

The following options are available for the `usermod` command:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`-a`** | **`--append`** | Add the user to supplementary group(s). Use only with the `-G` option. |
| **`-b`** | **`--badname`** | Allow names that do not conform to standards. |
| **`-c`** | **`--comment COMMENT`** | Update the comment field of the user in `/etc/passwd`. |
| **`-d`** | **`--home HOME_DIR`** | Set the user's new login directory. Use with `-m` to move current home contents. |
| **`-e`** | **`--expiredate EXPIRE_DATE`** | Specifies the date on which the user account will be disabled (YYYY-MM-DD or days since 1970-01-01). |
| **`-f`** | **`--inactive INACTIVE`** | Defines the number of days after a password exceeds its maximum age before the account becomes inactive. |
| **`-g`** | **`--gid GROUP`** | Sets the name or numerical ID of the user's new primary group. |
| **`-G`** | **`--groups GROUP1...`** | A list of supplementary groups. Use with `-a` to append instead of replacing the current list. |
| **`-l`** | **`--login NEW_LOGIN`** | Changes the user's login name from `LOGIN` to `NEW_LOGIN`. |
| **`-L`** | **`--lock`** | Locks a user's password (prepends a '!' to the encrypted password). Do not use with `-p` or `-U`. |
| **`-m`** | **`--move-home`** | Moves the content of the user's home directory to the new location (only valid with `-d`). |
| **`-o`** | **`--non-unique`** | Allows changing the user ID to a non-unique value (only valid with `-u`). |
| **`-p`** | **`--password PASSWORD`** | Defines a new encrypted password for the user. |
| **`-r`** | **`--remove`** | Remove the user from named supplementary group(s). Use only with the `-G` option. |
| **`-R`** | **`--root CHROOT_DIR`** | Apply changes within the `CHROOT_DIR` and use its configuration files. |
| **`-P`** | **`--prefix PREFIX_DIR`** | Apply changes to configuration files under a specific prefix directory. |
| **`-s`** | **`--shell SHELL`** | Changes the user's login shell. |
| **`-u`** | **`--uid UID`** | Sets the new numerical value of the user's ID. |
| **`-U`** | **`--unlock`** | Unlocks a user's password (removes the '!' prefix). Do not use with `-p` or `-L`. |
| **`-v`** | **`--add-subuids FIRST-LAST`** | Add a range of subordinate UIDs to the account. |
| **`-V`** | **`--del-subuids FIRST-LAST`** | Remove a range of subordinate UIDs from the account. |
| **`-w`** | **`--add-subgids FIRST-LAST`** | Add a range of subordinate GIDs to the account. |
| **`-W`** | **`--del-subgids FIRST-LAST`** | Remove a range of subordinate GIDs from the account. |
| **`-Z`** | **`--selinux-user SEUSER`** | Defines the SELinux user to be mapped with `LOGIN`. |
| **`--selinux-range SERANGE`** | N/A | Defines the SELinux MLS range (only valid if `-Z` is specified). |

---

## Caveats

* **Process Check:** Ensure the named user is not executing any processes when changing the UID, username, or home directory. The command checks this on Linux.
* **Cron/At Jobs:** You must manually change the owner of any crontab files or `at` jobs.
* **NIS Systems:** Any changes involving NIS must be performed on the NIS server.

---

## Configuration and Files

The following configuration variables in `/etc/login.defs` affect the behavior of this tool:

* `LASTLOG_UID_MAX`
* `MAIL_DIR`
* `MAIL_FILE`
* `MAX_MEMBERS_PER_GROUP`
* `SUB_GID_MIN`, `SUB_GID_MAX`, `SUB_GID_COUNT`
* `SUB_UID_MIN`, `SUB_UID_MAX`, `SUB_UID_COUNT`

**Files Modified:**

* `/etc/group`
* `/etc/gshadow`
* `/etc/login.defs`
* `/etc/passwd`
* `/etc/shadow`
* `/etc/subgid`
* `/etc/subuid`
