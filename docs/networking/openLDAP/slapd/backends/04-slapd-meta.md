# slapd-meta Command Documentation

**Description:** Performs basic LDAP proxying for a set of remote "targets," presenting their information as belonging to a single Directory Information Tree (DIT).

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapd-meta` backend is an enhancement of the `slapd-ldap` backend, specifically designed for proxying operations across multiple remote servers and performing naming context masquerading. It allows administrators to consolidate various remote LDAP servers into a single unified directory structure. The proxy instance must contain schema information for all attributes and objectClasses used in filters and request data; it is the administrator's responsibility to keep the local schema synchronized with the proxied servers. When looping back to the same `slapd` instance, each connection requires a new thread, which may necessitate tuning of the threads parameter or the use of `slapd-relay`.

---

## Options Reference

| Option | Long Form | Description |
| --- | --- | --- |
| conn-pool-max | - | Defines the maximum size of the privileged connections pool. |
| conn-ttl | - | Causes a cached connection to be dropped and recreated after a given time. |
| default-target | - | Rejects operations requiring a single target if none/multiple are selected, or marks a specific target as default. |
| dncache-ttl | - | Sets the time-to-live of the DN cache. |
| onerr | - | Determines behavior when an error is returned by a target (CONTINUE, report, or stop). |
| norefs | - | If yes, do not return search reference responses. |
| noundeffilter | - | If yes, return success instead of searching if a filter is undefined. |
| protocol-version | - | Indicates the protocol version used to contact the remote server. |
| pseudoroot-bind-defer | - | Defers authentication with the pseudo-root identity until actually needed. |
| quarantine | - | Sets retry intervals and attempt counts for URIs returning LDAP_UNAVAILABLE. |
| rebind-as-user | - | Remembers client bind credentials for rebinds, broken connections, or referral chasing. |
| session-tracking-request | - | Sends the client's IP, hostname, and identity to the remote server for information. |
| single-conn | - | Discards the current cached connection when the client rebinds. |
| use-temporary-conn | - | Creates a temporary connection when competing with other threads for a shared one. |

---

## Configuration / Files Modified

- `/usr/local/etc/openldap/slapd.conf`

---

## Examples

- **Scenario: Shared naming context**

```bash
database meta
suffix "dc=foo,dc=com"
uri "ldap://a.foo.com/dc=a,dc=foo,dc=com"
uri "ldap://b.foo.com/dc=b,dc=foo,dc=com"
```

- **Scenario: No shared naming context with suffixmassage**

```bash
database meta
suffix "dc=foo,dc=com"
uri "ldap://a.bar.com/dc=a,dc=foo,dc=com"
suffixmassage "dc=a,dc=foo,dc=com" "dc=bar,dc=org"
uri "ldap://b.foo.com/dc=b,dc=foo,dc=com"
suffixmassage "dc=b,dc=foo,dc=com" "o=Foo,c=US"
```

---

## Exit Values

- 0: Success (General behavior)
- Non-zero: Error (General behavior)

---

## Caveats / Notes

- **Overhead:** Naming context masquerading and multiple target proxying can result in excessive overhead for certain applications; use should be carefully considered.
- **Access Control:** This backend does not honor all ACL semantics defined in `slapd.access(5)`. Access checking is generally delegated to the remote servers. Only read access to entry pseudo-attributes and other attribute values returned by search operations is honored by the frontend.
- **Thread Tuning:** Looping back to the same instance requires additional threads. If multiple target features are not required, `slapd-relay` may be a better alternative as it reuses connections internally.
