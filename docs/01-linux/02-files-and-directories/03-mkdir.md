# mkdir Command Documentation

## Name and Synopsis

The `mkdir` command is used to create new directories.

**Basic Usage:**

```bash
mkdir [OPTION]... DIRECTORY...
```

---

## Description

The `mkdir` command creates the specified directory(ies) if they do not already exist. Mandatory arguments for long options are also required when using their corresponding short options.

---

## Options Reference

The following options are available for the `mkdir` command [6]:

| Option | Long Form | Description |
| :--- | :--- | :--- |
| **`-m`** | `--mode=MODE` | Sets the file mode (permissions) for the new directory, similar to the `chmod` command. This sets the mode directly rather than applying a umask. |
| **`-p`** | `--parents` | Creates parent directories as needed if they do not exist and suppresses error messages if the target directory already exists. Note that the file modes of parent directories are unaffected by any `-m` option. |
| **`-v`** | `--verbose` | Prints a descriptive message for every directory successfully created. |
| **`-Z`** | N/A | Sets the SELinux security context of each created directory to the default type. |
| **`--context[=CTX]`** | N/A | Similar to `-Z`. If a specific `CTX` is provided, it sets the SELinux or SMACK security context to that value. |
| **`--help`** | N/A | Displays the help message and exits. |
| **`--version`** | N/A | Outputs the version information and exits. |

---

## Exit Status

* **0**: Success
* **>0**: An error occurred (e.g., permission denied or invalid arguments)
