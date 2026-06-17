# fzf Command Documentation

## Name and Synopsis

`fzf` is a general-purpose command-line fuzzy finder and interactive terminal toolkit. It allows users to select items from a list using fuzzy matching, providing the building blocks to transform simple shell scripts into rich terminal applications.

```bash
find * -type f | fzf > selected
```

---

## Description

The primary purpose of `fzf` is to provide an interactive interface for filtering and selecting items from a stream of data. When provided with input via standard input (STDIN), it launches an interactive finder, processes the list, and writes the selected item to standard output (STDOUT). If no STDIN is provided, `fzf` defaults to traversing the file system under the current directory to generate the list of files.

The utility is optimized for speed, capable of processing millions of items in milliseconds. It supports various data types including files, process IDs, hostnames, and environment variables. Additionally, it includes a preview window system that can execute external programs (like `cat` or `bat`) to display content associated with the selected item in real-time.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `--height` | | Start `fzf` below the cursor with a specific height (e.g., `40%`). Use `~` to set maximum height. |
| `--popup` | | Start `fzf` in a popup window (requires tmux 3.3+ or Zellij 0.44+). |
| `--preview` | | Automatically starts an external process with the current line as the argument and shows the result in a split window. |
| `--layout` | | Change the layout of the interface (e.g., `reverse`). |
| `--border` | | Display a border around the selection area. |
| `--style` | | Use one of the style presets: `default`, `full`, or `minimal`. |
| `--delimiter` | | Specify a plain string as the delimiter for tokenization. |
| `--nth` | | Tokenize each line (note: can impact performance). |
| `--with-nth` | | Tokenize and reassemble each line (note: can impact performance). |
| `--ansi` | | Extract and parse ANSI color codes in the input (slower scanning). |
| `--exact` | | Start in "exact-match" mode where terms are not fuzzy matched and do not require quoting. |
| `--disabled` | | Disable secondary filtering (useful when integrating with tools like ripgrep). |

---

## Configuration / Files Modified

While `fzf` does not modify system configuration files, its behavior is heavily influenced by the following environment variables:

**General Options:**

* `FZF_DEFAULT_COMMAND`: The default command to use when input is a TTY.
* `FZF_DEFAULT_OPTS`: A string of default options (e.g., `--layout=reverse --inline-info`).
* `FZF_DEFAULT_OPTS_FILE`: Path to a file containing default options.

**Shell Integration Variables:**

* `FZF_CTRL_T_COMMAND`: Custom command for the CTRL-T key binding.
* `FZF_CTRL_R_COMMAND`: Custom command for the CTRL-R (history) key binding.
* `FZF_ALT_C_COMMAND`: Custom command for the ALT-C (directory) key binding.
* `FZF_COMPLETION_TRIGGER`: The sequence to trigger fuzzy completion (default is `**`).
* `FZF_COMPLETION_OPTS`: Options specifically for the fuzzy completion system.

---

## Examples

* **Basic file selection:**

```bash
find * -type f | fzf > selected
```

* **Open a file in Vim using fzf:**

```bash
vim $(fzf)
```

* **Fuzzy find with a preview window:**

```bash
fzf --preview 'cat {}'
```

* **Using a specific style and border:**

```bash
fzf --style full --border --padding 1,2 --input-label ' Input ' --header-label ' File Type ' --preview 'fzf-preview.sh {}'
```

---

## Exit Values

* 0: Success (item selected).
* Non-zero: Indicates an error or that no item was selected/cancelled by the user (General behavior).

---

## Caveats / Notes

* **Performance Warning:** Avoid adding the `--preview` option to the `FZF_DEFAULT_OPTS` environment variable, as it is not suitable for inputs other than file lists (e.g., process IDs or history entries).
* **Optimization:** Options such as `--ansi`, `--nth`, and `--with-nth` can significantly slow down initial scanning. Use them only when necessary.
* **Security/Robustness:** For more robust command substitution involving spaces or special characters, it is recommended to use `xargs` (e.g., `fzf --print0 | xargs -0 -o vim`) instead of standard shell variable expansion.
* **Shell Dependency:** Shell integration key bindings (CTRL-T, CTRL-R, ALT-C) vary by shell (Bash, Zsh, Fish, Nushell). Ensure the correct shell integration script is sourced in your configuration file.
