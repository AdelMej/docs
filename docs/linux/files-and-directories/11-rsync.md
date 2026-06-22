# rsync Command Documentation

## Name and Synopsis

rsync is a fast and extraordinarily versatile file copying tool designed for local and remote transfers. It utilizes a unique delta-transfer algorithm to minimize data transmission by only sending the differences between source and destination files.

**Basic Usage:**

```bash
rsync [OPTION...] SRC... [DEST]
```

---

## Description

Rsync is primarily used for backups, mirroring, and as an improved copy command for everyday use. It facilitates file transfers between local directories, across remote shells (such as SSH or RSH), or to/from a remote rsync daemon.

The tool employs a "quick check" algorithm by default, which identifies files needing transfer based on changes in size or last-modified time. When the quick check determines that a file's data does not need updating, other preserved attributes are updated directly on the destination. Key features include:

* Support for copying links, devices, owners, groups, and permissions.
* Exclude and include patterns similar to GNU tar.
* Ability to use transparent remote shells for secure transport.
* Pipelining of file transfers to minimize network latency.
* Support for anonymous or authenticated rsync daemons.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `-v` | `--verbose` | Increases the amount of information provided during transfer. |
| `-a` | `--archive` | Archive mode: preserves links, permissions, times, groups, owners, and devices (-rlptgoD). |
| `-r` | `--recursive` | Recurse into directories. |
| `-z` | `--compress` | Compresses file data during the transfer to save bandwidth. |
| `-n` | `--dry-run` | Performs a trial run without making any actual changes. |
| `-P` | `--partial --progress` | Keeps partially transferred files and shows progress during transfer. |
| `-h` | `--human-readable` | Outputs numbers in a human-readable format (e.g., K, M, G). |
| `-c` | `--checksum` | Skips files based on 128-bit checksums instead of size/mtime. |
| `-e` | `--rsh=COMMAND` | Specifies the remote shell program to use for communication. |
| `-I` | `--ignore-times` | Turns off "quick check" behavior, forcing all files to be updated. |
| `-u` | `--update` | Skips files that exist on the destination and are newer than the source. |
| `-D` | `--devices --specials` | Preserves device and special files (e.g., sockets, fifos). |
| `-l` | `--links` | Copies symbolic links as symlinks instead of ignoring them. |
| `-H` | `--hard-links` | Looks for hard-linked files in source and links them on destination. |

---

## Configuration / Files Modified

**System Files:**

* `/etc/rsyncd.conf`: The main configuration file for the rsync daemon.

**Relevant Configuration Variables:**

* `RSYNC_RSH`: Overrides the default shell used as transport.
* `RSYNC_PASSWORD`: Sets the password for connecting to an rsync daemon.
* `RSYNC_PROXY`: Redirects client connections through a web proxy.
* `RSYNC_CONNECT_PROG`: Defines the program to run for direct daemon connections.
* `RSYNC_PARTIAL_DIR`: Specifies the directory for partial transfers.
* `RSYNC_MAX_ALLOC`: Sets a limit on individual memory allocations.

---

## Examples

* **Backup a home directory via cron:**

```bash
rsync -aiz . bkhost:backup/joe/
```

* **Move files from a remote host while removing source files:**

```bash
rsync -aiv --remove-source-files rhost:/tmp/{file1,file2}.c ~/src/
```

* **Recursive transfer with compression and archive mode:**

```bash
rsync -avz foo:src/bar /data/tmp
```

* **List files instead of copying them:**

```bash
rsync -av --list-only foo* dest/
```

---

## Exit Values

* **0**: Success
* **1**: Syntax or usage error
* **2**: Protocol incompatibility
* **3**: Errors selecting input/output files, dirs
* **4**: Requested action not supported (e.g., 64-bit file manipulation on unsupported platforms)
* **5**: Error starting client-server protocol
* **6**: Daemon unable to append to log-file
* **10**: Error in socket I/O
* **11**: Error in file I/O
* **12**: Error in rsync protocol data stream
* **13**: Errors with program diagnostics
* **14**: Error in IPC code
* **20**: Received SIGUSR1 or SIGINT
* **21**: Some error returned by waitpid()
* **22**: Error allocating core memory buffers
* **23**: Partial transfer due to error
* **24**: Partial transfer due to vanished source files
* **25**: The `--max-delete` limit stopped deletions
* **30**: Timeout in data send/receive
* **35**: Timeout waiting for daemon connection

---

## Caveats / Notes

* **Safety Warning:** Use the `--dry-run` (-n) option before executing any command involving `--delete` to verify which files will be removed.
* **Filesystem Conflicts:** Exercise caution when copying from a case-preserving filesystem to a case-ignoring filesystem; filenames may overlap or fail to update correctly.
* **Privileges:** Options like `--owner`, `--group`, and `--devices` typically require super-user privileges. Use `--fake-super` to simulate these activities without root access by utilizing extended attributes.
* **Manual Cleanup:** When using `--ignore-times` (-I), rsync will update every file regardless of size or timestamp, which may increase transfer time significantly.
