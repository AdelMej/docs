# passwd Command Documentation

## Name and Synopsis

The `passwd` command is used to change passwords for user accounts on the system.

**Basic Usage:**

```bash
passwd [options] [LOGIN]
```

---

## Description

The `passwd` command allows users to manage account passwords. A regular user is restricted to changing only their own password, whereas the superuser can change the password for any account on the system. The utility also manages password validity periods and aging information.

When an account has a non-empty password, the system prompts the user for their current password; if incorrect, it allows only one attempt before failing. After verification, the user is prompted twice to enter a new replacement password, which must match exactly and meet system complexity requirements. The command also validates whether the user is permitted to change the password based on established aging policies.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `-a` | `--all` | Show status for all users (only used with -S). |
| `-d` | `--delete` | Deletes a user's password, making the account passwordless. |
| `-e` | `--expire` | Immediately expires an account's password, forcing a change at next login. |
| `-h` | `--help` | Display help message and exit. |
| `-i` | `--inactive INACTIVE` | Disable an account after the password has been expired for a specific number of days. |
| `-k` | `--keep-tokens` | Perform password change only for expired authentication tokens. |
| `-l` | `--lock` | Lock the named account's password by adding a "!" prefix. |
| `-n` | `--mindays MIN_DAYS` | Set the minimum number of days between password changes. |
| `-q` | `--quiet` | Operate in quiet mode. |
| `-r` | `--repository REPOSITORY` | Change the password in a specific repository. |
| `-R` | `--root CHROOT_DIR` | Apply changes within a specified chroot directory. |
| `-P` | `--prefix PREFIX_DIR` | Apply changes to configuration files under a root filesystem found under a prefix directory. |
| `-S` | `--status` | Display account status information consisting of 7 fields. |
| `-u` | `--unlock` | Unlock the password of the named account to its previous value. |
| `-w` | `--warndays WARN_DAYS` | Set the number of warning days before a password change is required. |
| `-x` | `--maxdays MAX_DAYS` | Set the maximum number of days a password remains valid (-1 removes checking). |
| `-s` | `--stdin` | Read the new password from standard input (e.g., via a pipe). |

---

## Configuration / Files Modified

The `passwd` command interacts with and modifies the following system files:

* `/etc/passwd`: Contains user account information.
* `/etc/shadow`: Contains secure, encrypted user password information.
* `/etc/pam.d/passwd`: Contains PAM configuration for the passwd command.

---

## Examples

* **Change your own password:**

    ```bash
    passwd
    ```

* **Root changing another user's password:**

    ```bash
    passwd username
    ```

* **Lock a specific user's account:**

    ```bash
    passwd -l username
    ```

* **Delete a user's password:**

    ```bash
    passwd -d username
    ```

* **Display the status of all accounts:**

    ```bash
    passwd -a -S
    ```

---

## Exit Values

* **0**: Success.
* **1**: Permission denied.
* **2**: Invalid combination of options.
* **3**: Unexpected failure, nothing done.
* **4**: Unexpected failure, passwd file missing.
* **5**: Passwd file busy, try again.
* **6**: Invalid argument to option.
* **10**: An error was returned by pam(3).

---

## Caveats / Notes

* **Complexity:** Password complexity requirements may vary depending on the local system configuration.
* **PAM Integration:** The `passwd` command utilizes PAM (Pluggable Authentication Modules) for both authentication and password changes.
* **Locked Accounts:** Using the `-l` option locks the password but does not disable the account entirely; users may still be able to log in via other methods (e.g., SSH keys). To fully disable an account, administrators should use `usermod --expiredate 1`.
* **NIS Limitations:** Users may be unable to change passwords if NIS is enabled and they are not logged into the NIS server.
