# slapd-mdb Command Documentation

## Name and Synopsis

The `slapd-mdb` is the Memory-Mapped DB backend for the `slapd` daemon. It serves as the primary database backend for OpenLDAP.

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapd-mdb` backend utilizes the Lightning Memory-Mapped DB (LMDB) library to store data. It relies entirely on the underlying operating system for memory management and does not implement its own caching mechanism. Because of this efficiency, it is the recommended primary database backend for OpenLDAP. The mdb backend supports a hierarchical database layout, which allows for subtree renames.

---

## Options Reference

The following options apply to the `mdb` backend and must be placed after a "backend mdb" line in the configuration.

| Option | Long Form | Description |
| :--- | :--- | :--- |
| idlexp | | Specifies a power of 2 for the maximum size of an index slot (Default: 16). Range: 16-30. |
| checkpoint | | Specifies frequency for flushing disk buffers (requires `dbnosync`). Uses `_kbyte_` or `_min_` minutes. |
| dbnosync | | Prevents immediate on-disk synchronization with memory changes to improve performance at the expense of data security. |
| directory | | The directory where LMDB files and indexes are stored (Default: `/usr/local/var/openldap-data`). |
| envflags | | Finer-grained control for the LMDB library (e.g., `nosync`, `nometasync`, `writemap`, `mapasync`, `nordahead`). |
| index | | Specifies indices to maintain for given attributes (e.g., `pres`, `eq`, `approx`, `sub`). |
| maxentrysize | | The maximum size of an entry in bytes; 0 indicates unlimited (Default: 0). |
| maxreaders | | The maximum number of threads with concurrent read access (Default: 126). |
| maxsize | | The maximum size of the database in bytes (Default: 10485760 bytes). |
| mode | | The file protection mode for newly created database files (Default: 0600). |
| multival | | Thresholds (`hi`, `lo`) for splitting multivalued attributes into a separate table to improve performance. |
| rtxnsize | | Maximum number of entries to process in a single read transaction (Default: 10000). |
| searchstack | | Depth of the stack used for search filter evaluation; each level uses 512K bytes (Default: 16). |

---

## Configuration / Files Modified

* **Configuration File:** `/usr/local/etc/openldap/slapd.conf`
* **Data Directory:** Default is `/usr/local/var/openldap-data`.
* **Logic:** Options must follow a "backend mdb" or "database mdb" line in the configuration file.

---

## Examples

* **Basic mdb database configuration (General behavior):**

```bash
backend mdb
database mdb
directory /usr/local/var/openldap-data
maxsize 2147483648
index sub
```

---

## Exit Values

* **0**: Success.
* **Non-zero**: Indicates an error during database initialization or operation (General behavior).

---

## Caveats / Notes

* **Data Security:** Enabling `dbnosync` improves performance but may result in data loss if the operating system crashes before changes are flushed to disk.
* **Database Growth:** It is important to set `maxsize` to a value large enough to accommodate anticipated growth, as increasing this size later can be impractical under heavy load.
* **Index Rebuilding:** Changing index settings via `slapd.conf` requires rebuilding the indices using `slapindex(8)`.
* **Memory Usage:** The `searchstack` depth directly impacts memory usage per thread; a depth of 16 uses approximately 8MB per thread.
