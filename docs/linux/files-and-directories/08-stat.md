# stat Command Documentation

## Name and Synopsis

Displays file or file system status.

```bash
stat [OPTION]... FILE...
```

---

## Description

The `stat` command is used to display detailed status information for files or file systems . It can output various types of metadata, such as permissions, ownership, inode numbers, and timestamps. The command supports both standard human-readable formats and custom formatting via specific flags. When the `--file-system` flag is utilized, the command shifts from reporting individual file attributes to reporting the status of the filesystem.

---

## Options Reference

| Option | Long Form | Description |
|---|---|---|
| -L | --dereference | Follow symbolic links |
| -f | --file-system | Display file system status instead of file status |
| --cached=MODE | | Specify use of cached attributes (always, never, or default) |
| -c | --format=FORMAT | Use specified format instead of default; output newline after each use |
| --printf=FORMAT | | Similar to --format but interprets backslash escapes and omits mandatory trailing newline |
| -t | --terse | Print the information in terse form |
| --help | | Display help and exit |
| --version | | Output version information and exit |

---

## Configuration / Files Modified

This command is read-only and does not modify any system configuration files or filesystem metadata.

---

## Examples

* Display basic status of a file:

```bash
stat myfile.txt
```

* Display status in terse form:

```bash
stat -t myfile.txt
```

* Display the status of the root file system:

```bash
stat -f /
```

* Display specific attributes (Name and Size) using custom format:

```bash
stat --printf="%n %s\n" myfile.txt
```

---

## Exit Values

* 0: Success
* Non-zero: Error occurred (General behavior)

---

## Caveats / Notes

* **Shell Overrides:** Your shell may have its own version of `stat`, which usually supersedes the version described in the manual.
* **Mandatory Arguments:** Mandatory arguments for long options are also mandatory for short options.
