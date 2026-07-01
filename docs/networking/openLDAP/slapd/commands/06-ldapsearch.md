# ldapsearch Command Documentation

## Name and Synopsis

`ldapsearch` is an LDAP search tool that provides a shell-accessible interface to the `ldap_search_ext(3)` library call. It opens a connection to an LDAP server, performs a bind, and executes a search using specified parameters.

```bash
ldapsearch [-V[V]] [-d debuglevel] [-n] [-v] [-c] [-u] [-t[t]] [-T path] [-F prefix] [-A] [-L[L[L]]] [-S attribute] [-b searchbase] [-s {base|one|sub|children}] [-a {never|always|search|find}] [-l timelimit] [-z sizelimit] [-f file] [-M[M]] [-x] [-D binddn] [-W] [-w passwd] [-y passwdfile] [-H ldapuri] [-P {2|3}] [-e [!]ext[=extparam]] [-E [!]ext[=extparam]] [-o opt[=optparam]] [-O security-properties] [-I] [-Q] [-N] [-U authcid] [-R realm] [-X authzid] [-Y mech] [-Z[Z]] filter [attrs...]
```

---

## Description

The primary purpose of `ldapsearch` is to query LDAP directories. It uses a search filter conforming to RFC 4515; if no filter is provided, it defaults to `(objectClass=*)`. The tool retrieves specified attributes and displays results in an extended version of the LDAP Data Interchange Format (LDIF). If the `-A` option is used, only attribute names are returned without their values. The tool handles complex requirements such as sorting results, setting search scopes, and managing authentication via simple or SASL mechanisms.

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -V\[V\] | | Print version info. -VV exits after printing. |
| -d | debuglevel | Set the LDAP debugging level. |
| -n | | Show what would be done without performing the search (dry run). |
| -v | | Run in verbose mode with diagnostics to standard output. |
| -c | | Continuous operation mode; continues after errors (useful with -f). |
| -u | | Include the User Friendly Name of the Distinguished Name (DN) in output. |
| -t\[t\] | | Write non-printable values to temporary files. |
| -T | path | Specify the directory for temporary files. |
| -F | prefix | URL prefix for temporary files. |
| -A | | Retrieve attributes only (no values). |
| -L\[L\[L\]\] | | Control LDIF output format (LDIFv1, disable comments, disable version). |
| -S | attribute | Sort returned entries based on the specified attribute. |
| -b | searchbase | Specify the starting point for the search. |
| -s | {base\|one\|sub\|children} | Specify search scope (default is sub). |
| -a | {never\|always\|search\|find} | Specify how aliases are dereferenced. |
| -l | timelimit | Wait at most `timelimit` seconds for a search to complete. |
| -z | sizelimit | Retrieve at most `sizelimit` entries. |
| -f | file | Read a series of lines from a file, performing one search per line. |
| -M\[M\] | | Enable manage DSA IT control; -MM makes it critical. |
| -x | | Use simple authentication instead of SASL. |
| -D | binddn | Use the Distinguished Name to bind to the directory. |
| -W | | Prompt for simple authentication password. |
| -w | passwd | Use provided string as the password for simple authentication. |
| -y | passwdfile | Use contents of a file as the password for simple authentication. |
| -H | ldapuri | Specify URI(s) referring to the LDAP server(s). |
| -P | {2\|3} | Specify the LDAP protocol version. |
| -e / -E | ext\[=extparam\] | Specify general and search extensions respectively. |
| -o | opt\[=optparam\] | Specify `ldap.conf` options or specific overrides (e.g., nettimeout). |
| -O | security-properties | Specify SASL security properties. |
| -I | | Enable SASL Interactive mode (always prompt). |
| -Q | | Enable SASL Quiet mode (never prompt). |
| -N | | Do not use reverse DNS to canonicalize SASL host name. |
| -U | authcid | Specify authentication ID for SASL bind. |
| -R | realm | Specify the realm of authentication ID for SASL bind. |
| -X | authzid | Specify requested authorization ID for SASL bind. |
| -Y | mech | Specify the SASL mechanism to be used for authentication. |
| -Z\[Z\] | | Issue StartTLS operation; -ZZ requires success. |

---

## Configuration / Files Modified

* **Configuration:** The command references `ldap.conf(5)` for default values and specific `-o` overrides.
* **Files Modified:** This command does not modify system files; it only reads from the filesystem/network and may create temporary files (controlled by `-T`).

---

## Examples

* **Basic subtree search for a surname:**

    ```bash
    ldapsearch -LLL "(sn=smith)" cn sn telephoneNumber
    ```

* **Subtree search with user-friendly DNs and temporary files for binary data:**

    ```bash
    ldapsearch -LLL -u -t "(uid=xyz)" jpegPhoto audio
    ```

* **One-level search at a specific base with organization filters:**

    ```bash
    ldapsearch -LLL -s one -b "c=US" "(o=University*)" o description
    ```

---

## Exit Values

* **0:** No errors occurred.
* **Non-zero:** An error occurred; a diagnostic message is written to standard error.

---

## Caveats / Notes

* **Continuous Mode:** When using the `-f` option, use `-c` to ensure `ldapsearch` continues processing lines even after encountering an error.
* **Server Limits:** The server may impose maximum values for `timelimit` and `sizelimit` that can only be overridden by the root user.
* **Alias Dereferencing:** By default, aliases are never dereferenced.
* **Sorting Behavior:** Using `-S` forces all entries to be retrieved before being sorted and printed, which deviates from the default behavior of printing entries as they are received.
