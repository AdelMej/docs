# slapo-retcode Documentation

## Name and Synopsis

* The `slapo-retcode` is a return code overlay for the OpenLDAP `slapd` daemon, designed to simulate erroneous or unusual server responses (such as specific error codes, referrals, and excessive response times) to test client behavior.
* Basic Usage:

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The primary purpose of the `retcode` overlay is to provide a testing environment where administrators can verify how LDAP clients handle various server-side issues. The overlay operates using three distinct strategies: it can look up return code data for operations on a specific configurable subtree, convert objects inheriting from the `errAbsObject` class into responses during search results, or discover these objects from the underlying database to compute responses.

To avoid naming conflicts with other overlays or the underlying database, all configuration directives specific to this overlay must be prefixed with `retcode-`. The behavior of the overlay can be bypassed by using the `manageDSAit` control.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| retcode-parent | | Defines the parent DN where dynamically generated entries reside. Defaults to the database suffix. |
| retcode-item | | Defines a dynamic entry including RDN, error code, operation list (op), text message, referral (ref), sleep time, matched DN, unsolicited OID/data, and disconnect flags. |
| retcode-indir | | Enables the use of in-directory stored `errAbsObject` entries. Note: This may cause significant overhead. |
| retcode-sleep | | Defines a sleep time in seconds before handling any operation. A negative value generates a random sleep time between 0 and the absolute value provided. |

---

## Configuration / Files Modified

* **Configuration File:** `/usr/local/etc/openldap/slapd.conf`
* The overlay utilizes a specific "return code" schema which is loaded automatically by the module.

---

## Examples

* Basic configuration and inclusion of standard response codes:

```bash
overlay retcode
retcode-parent "ou=RetCodes,dc=example,dc=com"
include ./retcode.conf
```

* Simulating a success response after a 10-second delay:

```bash
retcode-item "cn=Success after 10 seconds" 0x00 sleeptime=10
```

* Simulating a timelimitExceeded response after a 10-second delay:

```bash
retcode-item "cn=Timelimit after 10 seconds" 0x03 sleeptime=10
```

---

## Exit Values

* **General behavior:** The overlay generates responses based on the configured `errCode`. These can include standard LDAP error codes, referrals, or specific diagnostic text messages.

---

## Caveats / Notes

* **Control Bypass:** Clients can bypass the overlay's behavior by using the `manageDSAit` control (RFC 3296).
* **Performance Warning:** Using `retcode-indir` may result in a lot of unnecessary overhead during operations.
* **Schema Status:** The return code schema is considered a work in progress and is specifically designed for use with this overlay only.
