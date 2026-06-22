# chmod Command Documentation

## Name and Synopsis

Changes file mode bits.

```bash
chmod [OPTION]... MODE[,MODE]... FILE...
```

## Description

The `chmod` command modifies file permissions using symbolic representations (e.g., `u+x`) or octal numbers (e.g., `755`). It controls access rights for the owner, group, and others by altering the file's mode bits.

## Options Reference

| Option | Long Form | Description |
|---|---|---|
| -c | --changes | Report only when a change is made |
| -f | --silent | Suppress most error messages |
| -v | --verbose | Output diagnostic for every file processed |
| --dereference | | Affect the referent of each symbolic link |
| -h | --no-dereference | Affect each symbolic link itself |
| -R | --recursive | Change files and directories recursively |
| --reference=RFILE | | Use RFILE's mode instead of MODE values |

## Configuration / Files Modified

Modifies filesystem permission bits (inodes).

## Examples

* Grant execute permission to owner:

```bash
chmod u+x script.sh
```

* Set octal permissions for a directory:

```bash
chmod 755 folder/
```

## Exit Values

* 0: Success
* Non-zero: Error occurred

## Caveats / Notes

`chmod` does not change the permissions of symbolic links; it changes the permissions of the pointed-to file instead.
