# tar Command Documentation

## Name and Synopsis

The `tar` command is an archiving utility used to store multiple files into a single archive file (or device) and to manipulate those archives.

```bash
# GNU-style usage
tar --create [--file ARCHIVE] [OPTIONS] [FILE...]
tar --extract [--file ARCHIVE] [OPTIONS] [MEMBER...]
tar --list [--file ARCHIVE] [OPTIONS] [MEMBER...]
tar --append [--file ARCHIVE] [OPTIONS] [FILE...]
tar --delete [--file ARCHIVE] [OPTIONS] [MEMBER...]
```

---

## Description

GNU `tar` is designed to handle archives located on either local or remote machines. It supports three different option styles: traditional (clustered), UNIX/short-option (prefixed with a single dash), and GNU/long-option (prefixed with double dashes). When executing the command, exactly one operation mode must be specified per execution (such as create, extract, list, append, or delete). By default, when creating archives, directories are processed recursively unless the `--no-recursion` option is explicitly provided.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -c | --create | Create a new archive; arguments supply the files to be archived |
| -x | --extract, --get | Extract files from an archive |
| -t | --list | List the contents of an archive |
| -r | --append | Append files to the end of an archive |
| -u | --update | Append files which are newer than the corresponding copy in the archive |
| -f | --file=ARCHIVE | Use specified archive file or device |
| -v | --verbose | Verbosely list files processed (maximum verbosity level is 3) |
| -z | --gzip | Filter the archive through gzip(1) |
| -j | --bzip2 | Filter the archive through bzip2(1) |
| -J | --xz | Filter the archive through xz(1) |
| -C | --directory=DIR | Change to DIR before performing any operations |
| -I | --use-compress-program=COMMAND | Filter data through a specified command |
| -p | --preserve-permissions | Set permissions of extracted files to those recorded in the archive (default for superuser) |

---

## Configuration / Files Modified (if applicable)

General behavior: This command modifies the filesystem by creating, updating, or deleting archive files. It may also modify file permissions and ownership during the extraction process. It does not modify standard system configuration variables like `/etc/login.defs`.

---

## Examples

* **Create an archive verbosely:**

```bash
tar -cvf etc.tar /etc
```

* **Extract files from an archive:**

```bash
tar -xvf archive.tar
```

* **List the contents of an archive:**

```bash
tar -tvf archive.tar
```

* **Append files to an existing archive:**

```bash
tar -rvf archive.tar new_file.txt
```

---

## Exit Values

* 0: Successful termination.
* 1: Some files differ (if using `--compare`) or some files were changed while being archived/appended/updated.
* 2: Fatal error; an unrecoverable error occurred.
* Non-zero: If a subprocess (such as a compression utility) fails, `tar` will exit with that specific non-zero code.

---

## Caveats / Notes

* **Compressed Archives:** Compressed archives cannot be concatenated using the `-A` option.
* **Relative Paths:** The behavior of archive members depends on whether absolute paths or relative paths were used during creation.
* **Remote Access:** When using a colon in the archive name, `tar` attempts to access the remote host via `rsh(1)` by default (though `ssh(1)` is commonly preferred via `--rsh-command`).
