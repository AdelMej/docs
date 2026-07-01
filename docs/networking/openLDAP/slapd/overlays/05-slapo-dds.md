# slapo-dds Documentation

## Name and Synopsis

The **slapo-dds** overlay implements Dynamic Directory Services (DDS) for `slapd`, allowing for the definition of objects with a limited lifetime as specified in RFC 2589.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The DDS overlay enables the creation of dynamic objects characterized by the `dynamicObject` objectClass. These objects are assigned a time-to-live (TTL) value, which determines how long the entry remains in the directory. This lifetime can be refreshed using a specific extended operation, where a Client Refresh Period (CRP) is set to preserve the object from expiration. The system calculates the expiration time by adding the TTL to the current time. Once an object expires without being refreshed, it is automatically deleted.

A key logic of this implementation is that if a dynamic object has subordinates that are also dynamic objects, the parent's lifetime is automatically prolonged until every subordinate has expired. To ensure the overlay can successfully perform automated deletions, the database must have a `rootdn` defined. Because the process involves frequent internal lookups and entry modifications, it is recommended to use this overlay with backends that offer high write performance.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `overlay dds` | N/A | Adds the DDS overlay to the current database. |
| `dds-max-ttl <time>` | N/A | Sets the maximum allowed TTL value. Defaults to 86400 (1 day). Must be between 86400 and 31557600. |
| `dds-min-ttl <time>` | N/A | Sets the minimum TTL value. If set to 0 (default), no lower limit is enforced. |
| `dds-default-ttl <time>` | N/A | Specifies the default TTL for newly created dynamic objects. If set to 0, `dds-max-ttl` is used. |
| `dds-interval <time>` | N/A | Defines the interval between expiration checks. Defaults to 1 hour. |
| `dds-tolerance <time>` | N/A | Adds an extra time buffer to the deletion timer. Defaults to 0. |
| `dds-max-dynamicObjects <num>` | N/A | Limits the maximum number of simultaneous dynamic objects allowed in a naming context. |
| `dds-state {TRUE\|false}` | N/A | Enables or disables the Dynamic Directory Services feature. Defaults to TRUE. |

---

## Configuration / Files Modified

**Configuration File:**

* `/usr/local/etc/openldap/slapd.conf`

**Configuration Variables:**

* `overlay dds`
* `dds-max-ttl`
* `dds-min-ttl`
* `dds-default-ttl`
* `dds-interval`
* `dds-tolerance`
* `dds-max-dynamicObjects`
* `dds-state`

---

## Examples

* **Basic overlay activation:**

```bash
overlay dds
```

* **Restricting refresh to authenticated clients (Access Control):**

```bash
access to attrs=entryTtl
     by users manage
     by * read
```

* **Excluding dynamic attributes from replication:**

```bash
syncrepl ...
     exattrs=entryTtl,entryExpireTimestamp
```

---

## Exit Values

* **General behavior:** As a configuration overlay, `slapo-dds` does not provide standard command-line exit codes. Configuration errors will result in the failure of the `slapd` service to start.

---

## Caveats / Notes

* **Deletion Timing:** There is no guarantee of immediate deletion upon expiration; clients should not rely on an object vanishing instantly.
* **Replication Constraints:** When replicating dynamic objects, you must explicitly exclude the `dynamicObject` class and the `entryTtl` and `entryExpireTimestamp` attributes.
* **Access Control:** It is recommended to prevent anonymous clients from refreshing dynamic objects through appropriate access control rules.
* **RootDN Requirement:** The database must have a `rootdn` specified, otherwise, the overlay will be unable to delete expired objects.
