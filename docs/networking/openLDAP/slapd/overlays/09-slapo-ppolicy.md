# slapo-ppolicy Documentation

## Name and Synopsis

* The `slapo-ppolicy` is a Password Policy overlay for the OpenLDAP `slapd` daemon, implementing the IETF Password Policy proposal for LDAP.
* Basic Usage:

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `ppolicy` overlay intercepts, decodes, and applies specific password policy controls to the backend database and user password fields. It provides mechanisms for password aging (minimum and maximum ages), password reuse and duplication control, account timeouts, mandatory password resets, acceptable password content, and grace logins. Users can be associated with different policies, and there is no limit to the number of policies that can be created.

The overlay requires a `rootdn` to be configured on the database. During password updates, any identity with `manage` access to the `userPassword` attribute is treated as a password administrator. While the `userPassword` attribute technically allows multiple values, this implementation enforces a single value for that attribute.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| ppolicy_default | | Specifies the DN of the `pwdPolicy` object to use when no specific policy is set on a user's entry. |
| ppolicy_forward_updates | | Forwards policy state changes (like failures or lockouts) to a provider instead of the local database (requires replication setup). |
| ppolicy_hash_cleartext | | Hashes cleartext passwords in Add and Modify requests before storage; violates X.500/LDAP model but may be needed for certain clients. |
| ppolicy_use_lockout | | Includes the `AccountLocked` error code when a client binds to a locked account. |
| ppolicy_send_netscape_controls | | Sends Netscape password policy expired and expiring controls when appropriate. |
| ppolicy_check_module | | Specifies the path of a loadable module containing a `check_password()` function for additional quality checks. |

---

## Configuration / Files Modified

* **Configuration File:** `/usr/local/etc/openldap/slapd.conf`
* **Object Classes:** Requires the `pwdPolicy` (auxiliary) and `pwdPolicyChecker` (auxiliary) object classes.
* **Attributes:** Uses `pwdPolicySubentry` to link users to specific policies.

---

## Examples

* Basic configuration with a default policy:

```bash
database mdb
suffix dc=example,dc=com
...
overlay ppolicy
ppolicy_default "cn=Standard,ou=Policies,dc=example,dc=com"
```

---

## Exit Values

* **General behavior:** The overlay returns standard LDAP error codes for policy violations. If `ppolicy_use_lockout` is enabled, it specifically returns the `AccountLocked` error code during bind attempts to locked accounts.

---

## Caveats / Notes

* **Rootdn Requirement:** Some policies do not take effect when operations are performed with the `rootdn` identity.
* **Security Warning:** Enabling `ppolicy_hash_cleartext` is recommended only if search, compare, and read access are denied to all directory users.
* **Attacker Information:** Enabling `ppolicy_use_lockout` provides useful information to potential attackers; it should be disabled on sites sensitive to such security issues.
* **ACL Preference:** The use of the `pwdAllowUserChange` attribute is discouraged because OpenLDAP provides fine-grained Access Control Lists (ACLs).
* **Replication:** Policy behavior in replication environments is not guaranteed and is subject to change until a formal specification emerges.
