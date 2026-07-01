# slapo-auditlog Documentation

## Name and Synopsis

The **slapo-auditlog** is an Audit Logging overlay for `slapd` used to record all changes made to a specific backend database into a designated log file.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The Audit Logging overlay records every change occurring on a given backend database into a specified log file. These changes are logged in standard LDIF format, supplemented by unique comment headers that provide six specific fields of metadata regarding the operation. This includes the operation type, timestamp, database suffix, modifier name, originating IP address/port, and connection number. For Add and Modify operations, the identity of the user is retrieved from the `modifiersName` associated with the operation; while this typically reflects the requester's identity, it may be altered by other overlays.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `auditlog` | N/A | Specifies the fully qualified path for the log file. |
| `olcAuditlogFile` | N/A | Specifies the log file path when using `cn=config`. |

---

## Configuration / Files Modified

**Configuration Files:**

* `/usr/local/etc/openldap/slapd.conf` (Default configuration file)
* `/usr/local/etc/openldap/slapd.d` (Configuration directory for `cn=config`)

**Configuration Variables:**

* `auditlog`
* `olcAuditlogFile`

---

## Examples

* **Adding the overlay to cn=config:**

```bash
dn: olcOverlay=auditlog,olcDatabase={1}mdb,cn=config
changetype: add
objectClass: olcOverlayConfig
objectClass: olcAuditLogConfig
olcOverlay: auditlog
olcAuditlogFile: /tmp/auditlog.ldif
```

* **Example of a recorded changelog entry:**

```bash
# modify 1614223245 dc=example,dc=com cn=admin,dc=example,dc=com IP=[::1]:47270 conn=1002
dn: uid=joepublic,ou=people,dc=example,dc=com
changetype: modify
replace: displayName
displayName: Joe Public
-
replace: entryCSN
entryCSN: 20210225032045.045229Z#000000#001#000000
-
replace: modifiersName
modifiersName: cn=admin,dc=example,dc=com
-
replace: modifyTimestamp
modifyTimestamp: 20210225032045Z
-
# end modify 1614223245
```

---

## Exit Values

* **General behavior:** As a configuration overlay, `slapo-auditlog` does not provide standard command-line exit codes. Configuration errors will result in the failure of the `slapd` service to start.

---

## Caveats / Notes

* **Identity Source:** The modifier identity is derived from `modifiersName`. If other overlays are active that modify this attribute, the logged identity may differ from the actual requester's identity.
* **Log Format:** The output is stored as standard LDIF, ensuring compatibility with standard LDAP tools for review.
