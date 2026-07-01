# slapd-config Documentation

The `slapd-config` serves as the configuration backend for the `slapd` daemon. It manages all configuration information used by the `slapd` server and various associated tools, including `slapacl`, `slapadd`, `slapauth`, `slapcat`, `slapdn`, `slapindex`, `slapmodify`, and `slaptest`.

### Name and Synopsis

The config backend provides a structured method for managing LDAP server settings, allowing for dynamic configuration changes at runtime.

```bash
/usr/local/etc/openldap/slapd.d
```

---

### Description

The config backend manages the lifecycle of configuration data for the `slapd` daemon. It is designed to be backward compatible with the older `slapd.conf` file format but introduces the ability to modify configurations dynamically while the server is running. For these dynamic changes to persist across server restarts, the server must be run using a `slapd.d` configuration directory.

Unlike other backends, there can only be one instance of the config backend. The database root is hardcoded to `cn=config`, which contains global settings. The structure is organized into specific subtrees:

* **cn=Module**: Dynamically loaded modules.
* **cn=Schema**: Schema definitions (hardcoded and user-defined).
* **olcBackend**: Settings specific to a single backend type.
* **olcDatabase**: Settings specific to individual database instances, including the "frontend" database which provides inherited settings for other databases.

---

### Options Reference

The following table lists primary global and database configuration options.

| Option | Long Form | Description |
| :--- | :--- | :--- |
| olcAllows | | Specifies a set of features to allow (e.g., `bind_v2`, `bind_anon_cred`). |
| olcArgsFile | | The absolute name of a file holding the slapd command line arguments. |
| olcAuthzPolicy | | Specifies rules for Proxy Authorization (e.g., `none`, `from`, `to`, `any`, `all`). |
| olcConcurrency | | Hint for the desired level of concurrency for the underlying thread system. |
| olcConnMaxPending | | Maximum number of pending requests for an anonymous session (Default: 100). |
| olcConnMaxPendingAuth | | Maximum number of pending requests for an authenticated session (Default: 1000). |
| olcGentleHUP | | If TRUE, SIGHUP causes a "gentle" shutdown, stopping new connections without closing current ones. |
| olcIdleTimeout | | Seconds to wait before forcibly closing an idle client connection (Default: 0). |
| olcLogLevel | | Sets the level of debugging statements and statistics logged to syslog. |
| olcMaxFilterDepth | | Maximum depth of nested filters in search requests (Default: 1000). |
| olcPidFile | | The absolute name of the file holding the slapd process ID. |
| olcReferral | | The referral URL to pass back when a local database cannot handle a request. |
| olcRootDSE | | Name of the LDIF file containing user-defined attributes for the root DSE. |
| olcServerID | | Integer ID (0-4095) for the server, used in multi-provider replication. |
| olcThreads | | Maximum size of the primary thread pool (Default: 16). |
| olcWriteTimeout | | Seconds to wait before closing a connection with an outstanding write. |
| olcTLSCipherSuite | | Defines accepted ciphers and preference order for TLS/SSL. |
| olcTLSVerifyClient | | Checks performed on client certificates (`never`, `allow`, `try`, `demand`). |
| olcAccess | | Grants access to entries/attributes based on requestor identity and control level. |
| olcDefaultSearchBase | | Default search base for non-base search requests with an empty base DN. |
| olcPasswordHash | | Configures hashes for user passwords (e.g., `{SSHA}`, `{SHA}`, `{CRYPT}`). |
| olcReadOnly | | Puts the database into "read-only" mode; modifies return "unwilling to perform". |
| olcRequires | | Specifies conditions required for operations (e.g., `bind`, `authc`, `sasl`). |
| olcRestrict | | List of restricted operations (e.g., `add`, `bind`, `search`) per database. |
| olcSuffix | | The DN suffix of queries passed to the backend database. |
| olcSyncrepl | | Configures the database as a consumer using the LDAP Content Synchronization protocol. |

---

### Configuration / Files Modified

* **Configuration Directory:** `/usr/local/etc/openldap/slapd.d` (Required for dynamic changes and modern configuration).
* **Legacy Configuration File:** `/usr/local/etc/openldap/slapd.conf`.
* **Internal Structure:** Modifications affect the `cn=config` subtree, including `cn=Module`, `cn=Schema`, `olcBackend`, and `olcDatabase`.

---

### Examples

* **Initializing configuration from an LDIF file:**

```bash
slapadd -F /usr/local/etc/openldap/slapd.d -n 0 -l config.ldif
```

* **Converting a legacy `slapd.conf` to the new directory format:**

```bash
slaptest -f /usr/local/etc/openldap/slapd.conf -F /usr/local/etc/openldap/slapd.d
```

---

### Exit Values

* **0**: Success.
* **Non-zero**: Indicates an error during configuration parsing or tool execution (e.g., `slapadd` failure). *(General behavior)*

---

### Caveats / Notes

* **Uniqueness:** There can only be one instance of the config backend.
* **Dynamic Changes:** Changes made while `slapd` is running using only a `slapd.conf` file will not persist after a restart.
* **Indexing:** Changing indexing options (like `olcIndexHash64`) generally requires deleting existing indices and recreating them with `slapindex`.
* **Security:** The `olcAccess` controls on the config database are restrictive by default; only the root DN should typically have access.
