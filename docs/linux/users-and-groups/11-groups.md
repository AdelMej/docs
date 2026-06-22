# groups Command Documentation

## Name and Synopsis

The `groups` command prints the group memberships for a specified user or, by default, for the current process.

```bash
groups [OPTION]... [USERNAME]...
```

---

## Description

The primary purpose of the `groups` command is to identify the groups associated with specific user accounts. When executed without any arguments, the command displays the group memberships of the current process. It is important to note that the output for the current process may differ from the system's groups database if updates have occurred since the process was initialized.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `--help` | | Display help information and exit. |
| `--version` | | Output version information and exit. |

---

## Configuration / Files Modified

This command does not modify any configuration files. It retrieves information from the system's group database (typically `/etc/group`).

---

## Examples

* Display groups for the current user:

```bash
groups
```

* Display groups for a specific user (e.g., "username"):

```bash
groups username
```

---

## Exit Values

* 0: Success.
* Non-zero: Indicates an error occurred (General behavior).

---

## Caveats / Notes

The group memberships displayed for the current process reflect the state of the process at its start; changes to the system's groups database may not be reflected until a new process is started.
