# rmdir Command Documentation

## Name and Synopsis

The `rmdir` command is used to remove empty directories.

**Basic Usage:**

```bash
rmdir [OPTION]... DIRECTORY...
```

---

## Description

The `rmdir` command removes the specified directory(ies), provided that they are empty.

---

## Options Reference

The following options are available for the `rmdir` command:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`--ignore-fail-on-non-empty`** | N/A | Ignore each failure to remove a directory that is not empty. |
| **`-p`** | **`--parents`** | Remove the specified directory and its ancestors (e.g., `rmdir -p a/b` is similar to `rmdir a/b a`). |
| **`-v`** | **`--verbose`** | Output a diagnostic message for every directory processed. |
| **`--help`** | N/A | Display the help message and exit. |
| **`--version`** | N/A | Output version information and exit. |
