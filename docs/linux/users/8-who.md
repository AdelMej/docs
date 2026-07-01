# who Command Documentation

## Name and Synopsis

The `who` command displays information about users currently logged into the system.

```bash
who [OPTION]... [ FILE | ARG1 ARG2 ]
```

---

## Description

The primary purpose of the `who` command is to identify and provide details regarding active user sessions on a system. By default, it prints the username, the terminal line, and the login time. If no file is specified as an argument, the command defaults to reading from `/var/run/utmp`.

The command provides various levels of detail, ranging from a short summary of names and lines to a comprehensive list including boot times, runlevels, and process statuses. It can also be used to canonicalize hostnames via DNS or to check for specific message statuses associated with users.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `-a` | `--all` | Equivalent to `-b -d --login -p -r -t -T -u`. |
| `-b` | `--boot` | Print the time of the last system boot. |
| `-d` | `--dead` | Print information about dead processes. |
| `-H` | `--heading` | Print a line of column headings. |
| `-l` | `--login` | Print system login processes only. |
| `--lookup` | | Attempt to canonicalize hostnames via DNS. |
| `-m` | | Only show hostname and user associated with standard input. |
| `-p` | `--process` | Print active processes spawned by init. |
| `-q` | `--count` | Display all login names and the total number of users logged on. |
| `-r` | `--runlevel` | Print the current system runlevel. |
| `-s` | `--short` | Print only name, line, and time (Default behavior). |
| `-t` | `--time` | Print the last system clock change. |
| `-T, -w` | `--mesg` | Add user's message status as +, -, or ?. |
| `-u` | `--users` | List users logged in, including idle time. |
| `--message` | | Same as `-T`. |
| `--writable` | | Same as `-T`. |
| `--help` | | Display help text and exit. |
| `--version` | | Output version information and exit. |

---

## Configuration / Files Modified

The `who` command does not modify any configuration files or system states; it is a read-only utility. It primarily interacts with the following system files:

* `/var/run/utmp` (Default input file)
* `/var/log/wtmp` (Commonly used via FILE argument)

---

## Examples

* Display default logged-in users:

```bash
who
```

* Display login names and the total count of users:

```bash
who -q
```

* Display users with idle time information:

```bash
who -u
```

* Display information with column headings:

```bash
who -H
```

---

## Exit Values

* 0: Success.
* Non-zero: Indicates an error occurred (General behavior).

---

## Caveats / Notes

* **File Defaults:** If no `FILE` is specified, the command automatically uses `/var/run/utmp`.
* **Argument Handling:** If two arguments are provided without a file name (ARG1 ARG2), the command assumes the `-m` option is active.
