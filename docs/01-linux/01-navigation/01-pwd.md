# pwd Command Documentation

## Name and Synopsis

The `pwd` command is used to print the name of the current/working directory.

**Basic Usage:**

```bash
pwd [OPTION]...
```

---

## Description

The `pwd` command prints the full filename of the current working directory.

By default, if no option is specified, the command assumes the `-P` (physical) flag.

---

## Options Reference

The following options are available for the `pwd` command:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`-L`** | `--logical` | Use the PWD from the environment, even if it contains symbolic links. |
| **`-P`** | `--physical` | Resolve all symbolic links to show the physical path. |
| **`--help`** | N/A | Display help message and exit. |
| **`--version`** | N/A | Output version information and exit. |

---

## Important Notes

* **Shell Version:** Your shell may have its own built-in version of `pwd`, which usually supersedes the version described in this manual page. For specific details on available options provided by your shell, please refer to your shell's specific documentation.
