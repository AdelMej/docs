# diff Command Documentation

## Name and Synopsis

diff - compare files line by line

**Basic Usage:**

```bash
diff [OPTION]... FILES
```

---

## Description

The `diff` command compares two or more files or directories line by line. It is a fundamental utility for identifying differences in text content. The command provides various output formats, including normal diffs, brief reports, context-aware unified diffs, and side-by-side comparisons. It can also be used to generate scripts for editors (like `ed`) or report identical files.

Key behaviors include:

* Support for recursive directory comparison.
* Options to ignore case differences, trailing whitespace, or blank lines.
* Ability to specify input files via a "from-file" or "to-file" argument.
* Advanced formatting options for customizing the output of common and changed lines.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `-q` | `--brief` | Report only whether files differ |
| `-s` | `--report-identical-files` | Also report if two files are the same |
| `-c` | `-C NUM`, `--context[=NUM]` | Output NUM (default 3) lines of copied context |
| `-u` | `-U NUM`, `--unified[=NUM]` | Output NUM (default 3) lines of unified context |
| `-e` | `--ed` | Output an ed script |
| `-n` | `--rcs` | Output an RCS format diff |
| `-y` | `--side-by-side` | Output in two columns |
| `-W` | `--width=NUM` | Output at most NUM (default 130) print columns |
| `--left-column` | | Output only the left column of common lines |
| `--suppress-common-lines` | | Do not output common lines |
| `-p` | `--show-c-function` | Show which C function each change is in |
| `-F` | `--show-function-line=RE` | Show the most recent line matching RE |
| `--label` | | Use LABEL instead of file name and timestamp (can be repeated) |
| `-t` | `--expand-tabs` | Expand tabs to spaces in output |
| `-T` | `--initial-tab` | Make tabs line up by prepending a tab |
| `--tabsize=NUM` | | Tab stops every NUM (default 8) print columns |
| `--suppress-blank-empty` | | Suppress space or tab before empty output lines |
| `-l` | `--paginate` | Pass output through 'pr' to paginate it |
| `-r` | `--recursive` | Recursively compare any subdirectories found |
| `--no-dereference` | | Don't follow symbolic links |
| `-N` | `--new-file` | Treat absent files as empty |
| `--unidirectional-new-file` | | Treat absent first files as empty |
| `--ignore-file-name-case` | | Ignore case when comparing file names |
| `--no-ignore-file-name-case` | | Consider case when comparing file names |
| `-x` | `--exclude=PAT` | Exclude files that match PAT |
| `-X` | `--exclude-from=FILE` | Exclude files that match any pattern in FILE |
| `-S` | `--starting-file=FILE` | Start with FILE when comparing directories |
| `--from-file=FILE1` | | Compare FILE1 to all operands; FILE1 can be a directory |
| `--to-file=FILE2` | | Compare all operands to FILE2; FILE2 can be a directory |
| `-i` | `--ignore-case` | Ignore case differences in file contents |
| `-E` | `--ignore-tab-expansion` | Ignore changes due to tab expansion |
| `-Z` | `--ignore-trailing-space` | Ignore white space at line end |
| `-b` | `--ignore-space-change` | Ignore changes in the amount of white space |
| `-w` | `--ignore-all-space` | Ignore all white space |
| `-B` | `--ignore-blank-lines` | Ignore changes where lines are all blank |
| `-I` | `--ignore-matching-lines=RE` | Ignore changes where all lines match RE |
| `-a` | `--text` | Treat all files as text |
| `--strip-trailing-cr` | | Strip trailing carriage return on input |
| `-D` | `--ifdef=NAME` | Output merged file with '#ifdef NAME' diffs |
| `--GTYPE-group-format=GFMT` | | Format GTYPE input groups with GFMT |
| `--line-format=LFMT` | | Format all input lines with LFMT |
| `--LTYPE-line-format=LFMT` | | Format LTYPE input lines with LFMT |
| `-d` | `--minimal` | Try hard to find a smaller set of changes |
| `--horizon-lines=NUM` | | Keep NUM lines of the common prefix and suffix |
| `--speed-large-files` | | Assume large files and many scattered small changes |
| `--color[=WHEN]` | | Color output; WHEN is 'never', 'always', or 'auto'; plain --color means --color='auto' |
| `--palette=PALETTE` | | The colors to use when --color is active; PALETTE is a colon-separated list of terminfo capabilities |
| `--help` | | Display this help and exit |
| `-v` | `--version` | Output version information and exit |

---

## Configuration / Files Modified (if applicable)

This command does not modify system configuration files or user data; it only outputs the differences between provided inputs to standard output.

---

## Examples

* **Standard comparison:**

```bash
diff file1.txt file2.txt
```

* **Unified diff (commonly used for patches):**

```bash
diff -u file1.txt file2.txt
```

* **Brief report (only show if files differ):**

```bash
diff -q file1.txt file2.txt
```

* **Side-by-side comparison:**

```bash
diff -y file1.txt file2.txt
```

* **Recursive directory comparison:**

```bash
diff -r dir1/ dir2/
```

---

## Exit Values

* **0**: Inputs are the same
* **1**: Inputs are different
* **2**: Trouble occurred during execution

---

## Caveats / Notes

* **Mandatory Arguments:** Mandatory arguments to long options are also mandatory for short options.
* **Large Files:** Use the `--speed-large-files` option when dealing with large files and many scattered small changes to optimize performance.
* **Symlinks:** By default, `diff` does not follow symbolic links; use `--no-dereference` (default) or other options to control this behavior.
