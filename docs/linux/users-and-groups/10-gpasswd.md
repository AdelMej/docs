# gpasswd Command Documentation

## Name and Synopsis

The `gpasswd` command is used to administer `/etc/group` and `/etc/gshadow`. Every group can have administrators, members, and a password.

**Basic Usage:**

```bash
gpasswd [option] group
```

---

## Description

The primary purpose of the `gpasswd` command is the administration of group files. System administrators can use this utility to define specific group administrators and members, granting those users all rights associated with those roles.

When a password is set for a group, members are permitted to join the group using `newgrp(1)` without providing a password, while non-members are required to supply the password. If the command is called by a group administrator with only a group name, it prompts for a new group password.

---

## Options Reference

*Note: Except for the -A and -M options, the other options cannot be combined.*

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `-a` | `--add user` | Add the specified user to the named group. |
| `-d` | `--delete user` | Remove the specified user from the named group. |
| `-h` | `--help` | Display help message and exit. |
| `-Q` | `--root CHROOT_DIR` | Apply changes in the specified CHROOT_DIR directory (absolute paths only). |
| `-r` | `--remove-password` | Remove the password from the named group; only members will be allowed to use `newgrp` without a password. |
| `-R` | `--restrict` | Restrict access to the group by setting the password to "!". |
| `-A` | `--administrators user,...` | Set the list of administrative users for the group. |
| `-M` | `--members user,...` | Set the list of group members. |

---

## Configuration / Files Modified

This command modifies the following system files:

* `/etc/group`: Group account information.
* `/etc/gshadow`: Secure group account information.

The behavior of this tool is influenced by the following configuration variables in `/etc/login.defs`:

* `ENCRYPT_METHOD`: Defines the system default encryption algorithm for passwords.
* `MAX_MEMBERS_PER_GROUP`: Maximum members per group entry before a new line is started.
* `MD5_CRYPT_ENAB`: Indicates if passwords must be encrypted using the MD5-based algorithm (deprecated).
* `SHA_CRYPT_MIN_ROUNDS` / `SHA_CRYPT_MAX_ROUNDS`: Defines the number of SHA rounds used when `ENCRYPT_METHOD` is set to SHA256 or SHA512.

---

## Examples

* **Add a user to a group:**

```bash
gpasswd -a username groupname
```

* **Remove a user from a group:**

```bash
gpasswd -d username groupname
```

* **Set specific members for a group:**

```bash
gpasswd -M user1,user2 groupname
```

* **Remove the password from a group:**

```bash
gpasswd -r groupname
```

---

## Exit Values

* **0**: Success.
* **Non-zero**: Indicates an error occurred during execution (General behavior).

---

## Caveats / Notes

* **Scope Limitation:** This tool only operates on `/etc/group` and `/etc/gshadow`. It cannot change any NIS or LDAP groups, which must be managed on the corresponding server.
* **Security Warning:** Group passwords are considered an inherent security problem because more than one person is permitted to know the password.
