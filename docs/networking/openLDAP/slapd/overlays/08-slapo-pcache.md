# slapo-pcache Documentation

## Name and Synopsis

The **slapo-pcache** is a proxy cache overlay for `slapd` that allows for the caching of LDAP search requests in a local database.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The primary purpose of the **slapo-pcache** overlay is to improve performance by storing the results of common LDAP search queries in a local cache. When an incoming query arrives, the overlay determines if it matches a predefined "cacheable" template based on its filter string and the set of requested attributes. If a match is found in the cache, the result is returned immediately from the local database. Otherwise, the search is performed normally, and the results are saved in the cache for future use, provided they meet specific criteria such as entry limits. The overlay is specifically intended for use with `ldap`, `meta`, and `sql` backends, all of which require a configured `rootdn`.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `overlay pcache` | N/A | Adds the proxy cache overlay to the current backend. |
| `pcache <database> <max_entries> <numattrsets> <entry_limit> <cc_period>` | N/A | Enables proxy caching and defines general parameters: the internal database used, maximum entries before replacement occurs, number of attribute sets, entry limit for caching, and the consistency check period (in seconds). |
| `pcacheAttrset <index> <attrs...>` | N/A | Associates a set of attributes with an integer index. These indices are referenced by templates. Special characters like `*` (all user attributes) or `+` (all operational attributes) can be used. |
| `pcacheMaxQueries <queries>` | N/A | Sets the maximum number of queries to store in the cache (default is 10000). |
| `pcacheValidate {TRUE\|FALSE}` | N/A | If TRUE, checks if cached results are consistent with the proxy DSA's schema before returning them. |
| `pcacheOffline {TRUE\|FALSE}` | N/A | Sets the cache to offline mode; stops the consistency checker and prevents result expiration. |
| `pcachePersist {TRUE\|FALSE}` | N/A | If TRUE, saves cached queries across restarts to provide a "hot" startup. Configuration must remain identical across restarts. |
| `pcacheTemplate <template_string> <attrset_index> <ttl> [<negttl> [<limitttl> [<ttr>]]]` | N/A | Defines a cacheable template, its Time To Live (TTL), and optional parameters for caching negative results, size-limited results, and automatic refresh intervals. |
| `pcacheBind <filter_template> <attrset_index> <ttr> <scope> <base>` | N/A | Specifies a template for caching Simple Bind credentials. Credentials are stored as hashes, not cleartext. |
| `pcachePosition {head\|tail}` | N/A | Determines whether the response callback is placed at the head or tail of the callback list (default is `tail`). |

---

## Configuration / Files Modified

**Configuration File:**

* `/usr/local/etc/openldap/slapd.conf`

**Configuration Variables:**

* `overlay pcache`
* `pcache`
* `pcacheAttrset`
* `pcacheMaxQueries`
* `pcacheValidate`
* `pcacheOffline`
* `pcachePersist`
* `pcacheTemplate`
* `pcacheBind`
* `pcachePosition`

---

## Examples

* **Basic attribute set and template configuration:**

```bash
pcacheAttrset 0 mail postaladdress telephonenumber
pcacheTemplate (&(sn=)(givenName=)) 0 3600
```

* **Database configuration for the cache:**

```bash
directory /var/tmp/cache
maxsize 1073741824
```

---

## Exit Values

* **General behavior:** As a configuration overlay, `slapo-pcache` does not provide standard command-line exit codes. Configuration errors will typically result in the failure of the `slapd` service to start.

---

## Caveats / Notes

* **Data Inconsistency:** Caching is prone to inconsistencies as updates on the remote server may not be reflected until the TTL expires.
* **Paged Results:** The overlay strips out the `pagedResults` control because it requires a full result set to function properly.
* **Schema Requirements:** The proxy server must contain all schema information for the attributes used in query templates.
* **Security Risks:** Caching can potentially disclose data collected by one identity to another user if access controls differ. It is recommended to use `slapd-ldap` identity assertion with proxy caching to ensure security.
* **Indexing:** An equality index on the `pcacheQueryid` attribute should be configured in the local database to assist with removing expired entries.
