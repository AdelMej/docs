# whoami Command Documentation

## Name and Synopsis

The `whoami` command prints the effective user name associated with the current session's user ID.

**Basic Usage:**

```bash
whoami [OPTION]...
```

---

## Description

The primary purpose of the `whoami` command is to display the username of the current effective user. It functions identically to the `id -un` command. The utility retrieves the name from the system's user database based on the current process's effective user ID (EUID).

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `--help` | | Displays help information and exits. |
| `--version` | | Outputs version information and exits. |

---

## Configuration / Files Modified

This command is a query utility; it does not modify any configuration variables or system files.

---

## Examples

* **Display the current effective username:**

```bash
whoami
```

* **Display help options:**

```bash
whoami --help
```

* **Check the command version:**

```bash
whoami --version
```

---

## Exit Values

* **0**: Success.
* **Non-zero**: Indicates an error occurred during execution (General behavior).

---

## Caveats / Notes

* **Effective vs. Real User:** Note that `whoami` displays the *effective* user ID. If a user is executing a setuid or setgid program, `whoami` will return the name of the user owned by that program rather than the original user who launched it.
