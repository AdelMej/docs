# su Command Documentation

## Name and Synopsis

The `su` command allows commands to be run with a substitute user and group ID.

```bash
su [options] [-] [user|UID [argument...]]
```

---

## Description

The primary purpose of the `su` command is to allow users to execute commands under a different user or group identity. When called without a specified user, `su` defaults to running an interactive shell as the root user. If a user is specified, additional arguments can be passed directly to the shell.

For backward compatibility, `su` does not change the current directory by default and only sets the `HOME` and `SHELL` environment variables (plus `USER` and `LOGNAME` if the target user is not root). To avoid side effects caused by mixing environments, it is recommended to use the `--login` option.

This version of `su` utilizes PAM (Pluggable Authentication Modules) for authentication, account management, and session handling. Since version 2.38, `su` also automatically resets specific process resource limits (RLIMITs), including `RLIMIT_NICE`, `RLIMIT_RTPRIO`, `RLIMIT_FSIZE`, `RLIMIT_AS`, and `RLIMIT_NOFILE`. For privileged users or scripts executed by root, it is recommended to use `runuser(1)` or `setpriv(1)`.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `-c` | `--command` | Pass command to the shell; creates a new session via `setsid(2)`. |
| `-f` | `--fast` | Pass `-f` to the shell (utility varies by shell). |
| `-g` | `--group` | Specify the primary group (root user only). |
| `-G` | `--supp-group` | Specify a supplementary group (root user only). |
| `-, -l` | `--login` | Start shell as a login shell. Clears environment variables except TERM, COLORTERM, NO_COLOR, and whitelisted variables. Initializes HOME, SHELL, USER, LOGNAME, and PATH. |
| `-m, -p` | `--preserve-environment` | Preserve the entire environment; ignored if `--login` is specified. |
| `-P` | `--pty` | Create a pseudoterminal for the session to provide better security. |
| `-T` | `--no-pty` | Do not create a pseudo-terminal. |
| `-s` | `--shell` | Run the specified shell instead of the default. |
| `--session-command=command` | | Same as `-c`, but does not create a new session (discouraged). |
| `-w` | `--whitelist-environment` | Do not reset specified environment variables when clearing for `--login`. |
| `-h` | `--help` | Display help text and exit. |
| `-V` | `--version` | Display version information and exit. |

---

## Configuration / Files Modified

The `su` command reads configuration from the following files:

* `/etc/default/su`
* `/etc/login.defs`

Relevant configuration items:

* `FAIL_DELAY`: Delay in seconds for authentication failures.
* `ENV_PATH`: Defines the `PATH` environment variable for a regular user.
* `ENV_ROOTPATH` / `ENV_SUPATH`: Defines the `PATH` environment variable for root (`ENV_SUPATH` takes precedence).
* `ALWAYS_SET_PATH`: If set to "yes" and `--login`/`--preserve-environment` are not specified, `su` initializes `PATH`.

---

## Examples

* Run a specific command as root:

```bash
su -c "ls -l /root"
```

* Switch to another user's login shell:

```bash
su - username
```

* Run a specific shell as another user:

```bash
su -s /bin/zsh username
```

---

## Exit Values

* Returns the exit status of the executed command.
* If killed by a signal, returns the signal number plus 128.
* 1: Generic error before executing the requested command.
* 126: The requested command could not be executed.
* 127: The requested command was not found.

---

## Caveats / Notes

* **Security Warning:** If `su` shares a terminal with the original session, it may be vulnerable to privilege escalation via TIOCSTI/TIOCLINUX ioctl command injection. To mitigate this, use the `-c` option (new session) or the `--pty`/`-P` option.
* **Logging:** `su` logs failed login attempts to the `btmp` file but does not write to the `lastlog` file.
* **PAM Dependency:** This version uses PAM for final environment modifications and authentication; some behaviors may be managed via PAM configurations rather than command-line flags.
