# touch Command Documentation

## Name and Synopsis

The `touch` command is used to create a new file or change the timestamps of an existing file.

**Basic Usage:**

```bash
touch [OPTION]... FILE...
```

---

## Description

By default, `touch` updates the access and modification times of each specified file to the current time .

If a file argument does not exist, `touch` will create an empty file unless the `-c` or `-h` options are provided. Additionally, if the argument string is simply `-`, the command will change the times of the file associated with standard output.

---

## Options Reference

The following options are available for the `touch` command:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`-a`** | N/A | Change only the access time. |
| **`-c`** | `--no-create` | Do not create any files if they do not exist. |
| **`-d`** | `--date=STRING` | Parse a string and use it as the timestamp instead of the current time. |
| **`-f`** | N/A | (Ignored). |
| **`-h`** | `--no-dereference` | Affect each symbolic link instead of any referenced file; useful only on systems that can change the timestamps of a symlink. |
| **`-m`** | N/A | Change only the modification time. |
| **`-r`** | `--reference=FILE` | Use the timestamps of the specified reference file instead of the current time. |
| **`-t`** | N/A | Use a specified time in the format `[[CC]YY]MMDDhhmm[.ss]` (differs from `-d`). |
| **`--time=WORD`** | N/A | Specify which time to change: access time (`access`, `atime`, `use`) or modification time (`modify`, `mtime`). |
| **`--help`** | N/A | Display help message and exit. |
| **`--version`** | N/A | Output version information and exit. |

---

## Date String Format

When using the `--date=STRING` option, the string can be in a mostly free, human-readable format [2]. Examples include:

* Full dates: `"Sun, 29 Feb 2004 16:21:42 -0800"` or `"2004-02-29 16:21:42"`
* Relative dates: `"next Thursday"`
* Empty string: Indicates the beginning of the day.

---

## Important Notes

* **Mandatory Arguments:** Mandatory arguments to long options are also mandatory for short options.
* **Standard Output:** The `-` operand is handled specially and causes `touch` to change the times of the file associated with standard output.
