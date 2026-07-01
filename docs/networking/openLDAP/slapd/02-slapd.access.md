# slapd.access Documentation

## Overview

**slapd.access** is the configuration for access control in `slapd`, the stand-alone LDAP daemon. It defines who can perform which operations on specific entries or attributes within the directory.

## Configuration File

The configuration is typically located at:
`/usr/local/etc/openldap/slapd.conf`

## Description

The `slapd.conf` file contains global configuration options as well as database backend definitions. Both the global section and individual backend sections can contain access control directives.

- **Global Access:** Applies to all backends (including entries not held in any specific backend, like the root DSE).
- **Backend-Specific Access:** Applies only to entries belonging to that specific backend based on their naming context. If no specific rule is found, the global rules are used.
- **Default Policy:** If no access controls are defined, the default policy allows everyone to read everything but restricts updates to the `rootdn`.
- **Implicit Rules:** If a list is non-empty, it ends with an implicit "none" directive. If no directives apply to a backend, a default read is used.
- **RootDN Privilege:** The `rootdn` always has full read and write access to everything, bypassing ACL checks.

## Access Directive Syntax

The standard structure for an access control rule is:
`access to <what> [ by <who> [ <access> ] [ <control> ] ]+`

### 1. The <what\> Field (The Target)

Specifies the entity being accessed. It can take several forms:

- **dn\[.<dnstyle\>\]=<dnpattern\>**: Selects entries based on naming context.
  - *Styles*: `exact`, `base(object)`, `regex`, `one(level)`, `sub(tree)`, `children`.
  - *Note*: Using `regex` is flexible but can be slow and ambiguous; explicit styles are recommended for performance.
- **filter=<ldapfilter\>**: Selects entries based on a standard LDAP filter (e.g., `(objectClass=*)`).
- **attrs=<attrlist\>**: Selects specific attributes.
  - `@` prefix denotes an objectClass.
  - `!` prefix denotes attributes not required/allowed by that objectClass.

### 2. The <who\> Field (The Requestor)

Specifies who is requesting access. Examples include:

- `*`: Everybody.
- `anonymous`: Unauthenticated clients.
- `users`: Authenticated clients.
- `self`: The entry itself.
- `dn=<DN>`: A specific Distinguished Name.
- `group=<group>`: Requests originating from a specific group.
- `peername`, `sockname`, `domain`, `sockurl`: Access based on connection metadata (IP, hostname, etc.).

### 3. The <access\> Field (The Privilege)

Determines the level of permission granted:

- **Levels**: `none`, `disclose`, `auth`, `compare`, `search`, `read`, `write` (or `add`/`delete`), and `manage`.
- **Privileges**: Specific flags like `+w` (write), `+a` (add), `+z` (delete), `+s` (search), `+r` (read).

### 4. The <control\> Field (The Flow)

Controls how the daemon processes the list of rules:

- **stop** (Default): Stops checking access rules as soon as a match is found.
- **continue**: Continues checking other `<who>` clauses in the same statement to potentially increment privileges.
- **break**: Allows the daemon to continue processing subsequent `<access>` clauses even if the current one matched.

## Examples

**Granting write privileges to a specific Update DN:**

```bash
access to *
     by dn.exact="cn=The Update DN,dc=example,dc=com" write
     by * break
```

**Granting search and read privileges with subtree logic:**

```bash
access to dn.subtree="dc=example,dc=com" attrs=cn
     by * =cs break
access to dn.subtree="ou=People,dc=example,dc=com"
     by * +r
```

## Caveats and Notes

- **Regex Ambiguity:** Using `dn.regex` without anchors (like `^` and `$`) can cause unexpected matches. For example, a regex for `dc=example,dc=com` will match both `uid=joe,dc=example,dc=com` and `dc=example,dc=com,uid=joe`.
- **Performance:** Explicitly using styles like `subtree` or `exact` is significantly faster than using `regex` for simple matching.
- **Substring Expansion:** When using `regex`, submatches can be used in the `<who>` field using `${v_n_}` (attribute value) or `${d_n_}` (DN pattern).

# slapd.access Command Documentation
