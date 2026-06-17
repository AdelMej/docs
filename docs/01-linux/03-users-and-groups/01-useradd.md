# useradd Command Documentation

## Name and Synopsis

The `useradd` command is used to create a new user account or update default new user information.

**Basic Usage:**

```bash
useradd [options] LOGIN
useradd -D
useradd -D [options]
```

---

## Description

When invoked without the `-D` option, `useradd` creates a new user account using values specified on the command line plus system defaults. Depending on the options provided, it will update system files and may create the new user's home directory while copying initial files.

By default, a group is also created for the new user, depending on the `USERGROUPS_ENAB` variable in `/etc/login.defs` (or if the `-U` option is used).

---

## Options Reference

The following options are available for the `useradd` command:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`-b`** | **`--base-dir BASE_DIR`** | Sets the default base directory for home directories if `-d` is not specified. |
| **`-c`** | **`--comment COMMENT`** | Provides a short description or full name for the account. |
| **`-d`** | **`--home-dir HOME_DIR`** | Specifies the login directory for the new user. |
| **`-e`** | **`--expiredate EXPIRE_DATE`** | Sets the date on which the account will be disabled (YYYY-MM-DD or days since 1970-01-01). |
| **`-f`** | **`--inactive INACTIVE`** | Defines the number of days after a password expires before the account is locked. |
| **`-g`** | **`--gid GROUP`** | Sets the name or number of the user's primary group. |
| **`-G`** | **`--groups GROUP1...`** | Lists supplementary groups for the user. |
| **`-k`** | **`--skel SKEL_DIR`** | Specifies the skeleton directory for home directory contents (only valid with `-m`). |
| **`-K`** | **`--key KEY=VALUE`** | Overrides defaults in `/etc/login.defs` (e.g., `UID_MIN`, `UMASK`). |
| **`-m`** | **`--create-home`** | Creates the home directory if it doesn't exist and copies files from the skeleton directory. |
| **`-M`** | **`--no-create-home`** | Does not create the home directory, even if system-wide settings allow it. |
| **`-N`** | **`--no-user-group`** | Skips creating a group with the same name as the user. |
| **`-o`** | **`--non-unique`** | Allows creation of an account with an existing UID (valid only with `-u`). |
| **`-p`** | **`--password PASSWORD`** | Defines an initial encrypted password. |
| **`-r`** | **`--system`** | Creates a system account with specific UID/GID ranges and no aging information. |
| **`-s`** | **`--shell SHELL`** | Sets the path to the user's login shell. |
| **`-u`** | **`--uid UID`** | Sets the numerical value of the user's ID. |
| **`-U`** | **`--user-group`** | Creates a group with the same name as the user and adds the user to it. |
| **`-Z`** | **`--selinux-user SEUSER`** | Defines the SELinux user for the new account. |

---

## Changing Default Values

Using `useradd -D` displays current defaults, while using it with options updates them:

* **`-b`**: Sets the default home directory prefix.
* **`-e`**: Sets the default account expiry date.
* **`-f`**: Sets the default inactivity period.
* **`-g`**: Sets the default primary group.
* **`-s`**: Defines the default login shell.

---

## Exit Statuses

The `useradd` command exits with the following values:

* **0**: Success
* **1**: Can't update password file
* **2**: Invalid command syntax
* **3**: Invalid argument to option
* **4**: UID already in use (and `-o` was not used)
* **6**: Specified group doesn't exist
* **9**: Username or group name already in use
* **10**: Can't update group file
* **12**: Can't create home directory
* **14**: Can't update SELinux user mapping
* **19**: Invalid user or group name
