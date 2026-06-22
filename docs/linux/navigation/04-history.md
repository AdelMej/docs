# history Command Documentation

## Name and Synopsis

The `history` command displays the list of commands previously executed in the current shell session, typically accompanied by line numbers.

```bash
history [n]
history -c
history -d offset
history [-anrw] [filename]
history -ps arg
```

---

## Description

The primary purpose of the `history` command is to provide a chronological record of commands entered during a shell session. By default, it lists all commands with associated line numbers; entries marked with an asterisk (*) indicate that the command has been modified since it was first recorded.

If an argument `n` is provided, the command will only display the last `n` lines of the history. When using options like `-a`, `-n`, `-r`, or `-w` without a specified filename, the shell defaults to the path defined in the `HISTFILE` environment variable (typically `~/.bash_history`).

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `-c` | | Clear the entire history list. Can be combined with other options to completely replace the list. |
| `-d offset` | | Delete the history entry at the specified position (offset). |
| `-a` | | Append new lines entered since the start of the current session to the history file. |
| `-n` | | Append lines from the history file that have not yet been read into the current session's list. |
| `-r` | | Read the current history file and append its contents to the current history list. |
| `-w` | | Write the current history list out to the specified history file. |
| `-p` | | Perform history substitution on arguments and display the result without storing it in the history list. |
| `-s` | | Add the provided arguments to the end of the history list as a single entry without executing them. |

---

## Configuration / Files Modified

The `history` command interacts with the following environment variables and files:

**Relevant Environment Variables:**

* `HISTCONTROL`: Controls how history is stored (e.g., `ignorespace` to skip commands starting with a space, `erasedups` to remove duplicates).
* `HISTSIZE`: Defines the number of history lines to keep in memory (default is usually 500).
* `HISTFILESIZE`: Defines the number of history lines to save in the history file upon shell exit.
* `HISTFILE`: Specifies the path to the history file (default is `~/.bash_history`).
* `HISTTIMEFORMAT`: Sets the format for timestamps displayed with each history entry.

**Files Modified:**

* The command may write to or read from the file defined in `$HISTFILE`.

---

## Examples

* Display the last 10 commands:

```bash
history 10
```

* Clear the current session's history:

```bash
history -c
```

* Delete a specific command from history (e.g., at position 50):

```bash
history -d 50
```

* Search for a specific previous command:

```bash
history | grep "keyword"
```

* Save the entire history list to a text file:

```bash
history > cmds.txt
```

---

## Exit Values

* 0: Success (standard operation).
* Non-zero: Indicates an error, such as:
  * An invalid option was encountered.
  * An error occurred while reading from or writing to the history file.
  * An invalid offset was supplied for the `-d` option.
  * History expansion failed during a `-p` operation.

---

## Caveats / Notes

* **History Expansion:** The `!` character is used for history expansion (e.g., `!!` for the previous command, `!$` for the last argument). These are processed by the shell, not necessarily by the `history` builtin itself.
* **Shell Dependency:** While common across many shells, specific behaviors and available options may vary slightly between Bash, Zsh, and Tcsh.
* **Persistence:** Changes made to the history list in memory (like deletions) are only written to the disk file if the `-w` option is used or if the shell exits normally (depending on configuration).
