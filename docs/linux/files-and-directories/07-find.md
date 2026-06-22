# find Command Documentation

## Name and Synopsis

The `find` command is used to search for files in a directory hierarchy.

**Basic Usage:**

```bash
find [-H] [-L] [-P] [-D debugopts] [-Olevel] [starting-point...] [expression]
```

---

## Description

GNU `find` searches the directory tree rooted at each given starting-point by evaluating the provided expression from left to right until the outcome is known. If no starting-point is specified, the current directory (`.`) is assumed.

---

## Options Reference

The following options control the behavior of `find`:

| Option | Description | Detailed Explanation |
| :--- | :--- | :--- |
| **`-P`** | **Never Follow Links** | The default behavior; information is taken from the symbolic link itself. |
| **`-L`** | **Follow Links** | Information is taken from the file to which the link points. |
| **`-H`** | **Partial Link Following** | Do not follow symbolic links, except while processing command-line arguments. |
| **`-D debugopts`** | **Diagnostic Info** | Prints diagnostic information (e.g., `exec`, `opt`, `rates`, `search`, `stat`, `tree`). |
| **`-Olevel`** | **Query Optimisation** | Enables query optimization. Level 1 is the default; level 3 enables a full cost-based optimizer. |
| **`-depth`** | **Depth-First** | Process each directory's contents before the directory itself. This is implied by the `-delete` action. |
| **`-maxdepth levels`** | **Limit Depth** | Descend at most the specified number of levels below the starting points. |
| **`-mindepth levels`** | **Minimum Depth** | Do not apply tests or actions at levels less than the specified number. |
| **`-xdev`** | **Stay on Device** | Don't descend into directories on other devices. |
| **`-mount`** | **Ignore Other Devices** | Ignore files on other devices. |

---

## Expressions

The expression is composed of several types of components:

### Tests

Tests return true or false based on file properties. Examples include:

* **`-name pattern`**: Matches the base of the file name against a shell pattern.
* **`-type`**: Matches specific types like `f` (regular file), `d` (directory), or `l` (symbolic link).
* **`-size n`**: Matches files based on size (e.g., `-size +100M`).
* **`-mtime n`**: Matches files modified within a certain number of 24-hour periods.
* **`-executable`**: Matches files readable and executable by the current user.

### Actions

Actions have side effects. Examples include:

* **`-print`**: Prints the full file name followed by a newline.
* **`-print0`**: Prints the name followed by a null character (safer for filenames with spaces/newlines).
* **`-delete`**: Deletes files or directories; automatically implies `-depth`.
* **`-exec command ;`**: Executes a command for each matched file.
* **`-execdir command {} +`**: A more secure and efficient version of `-exec` that runs the command from the subdirectory containing the file.

### Operators

Operators join expressions together:

* **`!`**: Logical NOT.
* **`-a`**: Logical AND (implied if no operator is present).
* **`-o`**: Logical OR.
* **`,`**: List operator; evaluates both sides but only returns the value of the second.

---

## Examples

* **Find files modified in the last 24 hours:**
    `find $HOME -mtime 0`
* **Run `file` on every regular file in the current directory:**
    `find . -type f -exec file '{}' \;`
* **Safely delete files named "core":**
    `find /tmp -name core -type f -print0 | xargs -0 /bin/rm -f`

---

## Exit Status

* **0**: All files were processed successfully.
* **>0**: Errors occurred during processing.
