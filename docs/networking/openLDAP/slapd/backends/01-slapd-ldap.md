# slapd-ldap Documentation

**Description:** An LDAP backend that acts as a proxy to forward incoming requests to another LDAP server.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `slapd-ldap` backend forwards requests and chases referrals. It utilizes connection pooling to enhance efficiency by sharing connections for identical DNs. It can also assert the identity of locally authenticated clients to remote servers. Administrators must ensure the local proxy schema matches the proxied server's schema.

---

## Options Reference

| Option | Long Form | Description |
| --- | --- | --- |
| uri | - | LDAP server(s) to use (space/comma separated). |
| acl-bind | - | Authentication parameters for the proxy's identity. |
| cancel | - | Behavior for operation cancellation (abandon, ignore, or exop). |
| chase-referrals | - | Enable/disable automatic referral chasing. |
| conn-ttl | - | Time before a cached connection is dropped. |

---

## Configuration / Files Modified

- `/usr/local/etc/openldap/slapd.conf`

---

## Examples

- URI with multiple hosts:

```bash
uri "ldap://host1/ ldap://backup-host/"
```

---

## Exit Values

- 0: Success (General behavior)
- Non-zero: Error (General behavior)

---

## Caveats / Notes

- **Threading:** Looping back to the same slapd instance requires additional threads.
- **Schema:** The administrator is responsible for keeping schemas synchronized between servers.
