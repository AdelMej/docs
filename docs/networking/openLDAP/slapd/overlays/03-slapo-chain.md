# slapo-chain Documentation

## Name and Synopsis

The **slapo-chain** overlay enables automatic referral chasing within the LDAP server.

**Basic Usage:**

```bash
/usr/local/etc/openldap/slapd.conf
```

---

## Description

The `chain` overlay allows `slapd` to automatically follow referrals returned by a backend database. When a referral is received (except for bind operations), the overlay uses an instance of the `ldap` backend to chase that referral. If an operation is performed with a valid identity (following a successful bind), the system can assert that identity during the chasing process using proxied authorization controls \[RFC 4370\]. To avoid conflicts with other overlays or the underlying database, all configuration directives specific to this overlay are prefixed with `chain-`. Notably, this functionality is integrated into the LDAP backend itself rather than existing as a separate module.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| `overlay chain` | N/A | Adds the chain overlay to the current backend. Primarily intended for local storage backends that may return referrals. |
| `chain-cache-uri {FALSE\|true}` | N/A | Instructs the overlay to cache connections to URIs parsed from referrals that are not predefined, allowing them to be reused anonymously. |
| `chain-chaining [resolve=<r>] [continuation=<c>] [critical]` | N/A | Enables the experimental chaining control. Values for `r` and `c` can be `chainingPreferred`, `chainingRequired`, `referralsPreferred`, or `referralsRequired`. |
| `chain-max-depth <n>` | N/A | Sets the maximum depth for referral chasing. The default is 1, which effectively disables further chasing. |
| `chain-return-error {FALSE\|true}` | N/A | If TRUE, the real error is returned if referral chasing fails, rather than the original referral. |
| `chain-uri <ldapuri>` | N/A | Instantiates a new underlying `ldap` database to contact the specified URI. All subsequent `chain-` prefixed directives apply to this specific instance until another `chain-uri` is defined. |

---

## Configuration / Files Modified

**Configuration File:**

* `/usr/local/etc/openldap/slapd.conf`

**Configuration Variables:**

* `overlay chain`
* `chain-cache-uri`
* `chain-chaining`
* `chain-max-depth`
* `chain-return-error`
* `chain-uri`

---

## Examples

* **Basic configuration for a single trusted URI with identity assertion:**

```bash
overlay chain
chain-rebind-as-user FALSE
chain-uri "ldap://ldap1.example.com"
chain-rebind-as-user TRUE
chain-idassert-bind bindmethod="simple" binddn="cn=Auth,dc=example,dc=com" credentials="secret" mode="self"
```

* **Configuration for multiple trusted URIs with different assertion modes:**

```bash
overlay chain
chain-uri "ldap://ldap1.example.com"
chain-idassert-bind bindmethod="simple" binddn="cn=Auth,dc=example,dc=com" credentials="secret" mode="self"
chain-uri "ldap://ldap2.example.com"
chain-idassert-bind bindmethod="simple" binddn="cn=Auth,dc=example,dc=com" credentials="secret" mode="none"
```

---

## Exit Values

* **General behavior:** As a configuration overlay, `slapo-chain` does not provide standard command-line exit codes. Configuration errors will result in the failure of the `slapd` service to start.

---

## Caveats / Notes

* **Backend Compatibility:** This overlay is useless when used with the `slapd-ldap` and `slapd-meta` backends, as those backends already exploit the libldap specific referral chase feature.
* **Anonymous Chaining:** All URIs not explicitly listed in the configuration are chained anonymously.
* **Experimental Features:** The chaining control is currently experimental; its support and behavior may change in future releases.
* **Inheritance:** All `slapd-ldap` directives appearing before the first occurrence of a `chain-uri` are inherited by all URIs unless specifically overridden within an individual URI configuration.
