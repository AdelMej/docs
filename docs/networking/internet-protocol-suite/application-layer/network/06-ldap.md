# Lightweight Directory Access Protocol (LDAP)

Lightweight Directory Access Protocol (LDAP) is an Internet protocol used for accessing directory information services that operate in accordance with X.500 data and service models. While its name suggests a simple protocol, LDAP encompasses much more than just an API; it includes specifications for the operations of systems satisfying that API and defines a standardized model of the data to be managed by the directory service.

Directory services are essential for developing intranet and Internet applications as they allow for the sharing of information regarding users, systems, networks, services, and applications across a network. Examples include corporate email directories or telephone directories (lists of subscribers with addresses and phone numbers). A common application for LDAP is providing a centralized location to store usernames and passwords, allowing various applications and services to connect to an LDAP server to validate users.

LDAP is a simpler, "lightweight" subset of the X.500 series standards, specifically the X.511 Directory Access Protocol. Because of this relationship, it is sometimes referred to as "X.500 Lite."

## Main Components

The core components of LDAP include:

* **Protocol (RFC 4511):** An application protocol running over an Internet Protocol (IP) network used to communicate with a directory service.
* **Directory Information Models (RFC 4512):** A specification for systems that implement directory services, defined as a collection of open systems cooperating to provide those services.
* **Schema for User Applications (RFC 4519):** A standard and extensible data schema for information held in a directory service, specifying attribute types and object classes intended for use by LDAP.

## History

The concept of directory services originated from the telecommunications industry's experience managing telephone directories over 70 years ago. This input culminated in the comprehensive X.500 specification produced by the International Telecommunication Union (ITU) in the 1980s.

X.500 directory services were traditionally accessed via the X.511 Directory Access Protocol (DAP), which required the Open Systems Interconnection (OSI) protocol stack. LDAP was created as a lightweight alternative designed to access X.500 directory services through the simpler and more widespread TCP/IP protocol stack.

LDAP was originally created circa 1993 by Tim Howes, Steve Kille, Colin Robbins, and Wengyik Yeong as a successor to DIXIE and DAS. Work began in 1996 on LDAPv3 under the IETF, which was first published in 1997. LDAPv3 superseded LDAPv2, added support for extensibility, integrated the Simple Authentication and Security Layer (SASL), and better aligned the protocol with the 1993 edition of X.500.

Initially known as the Lightweight Directory Browsing Protocol (LDBP), it was renamed to LDAP to reflect its expanded scope beyond browsing to include directory update functions. It received its "Lightweight" name because it was less network-intensive than DAP, making it easier to implement over the Internet due to its modest bandwidth usage.

LDAP has influenced subsequent protocols such as XED, DSML, SPML, and SLP. It also serves as the basis for Microsoft's Active Directory.

## Protocol Overview

A client initiates an LDAP session by connecting to a Directory System Agent (DSA) on TCP or UDP port 389, or on port 636 for LDAPS (LDAP over TLS/SSL). The client sends operation requests, and the server provides responses. Except for specific instances, clients do not need to wait for a response before sending the next request, and the server may return responses in any order. All information is transmitted using Basic Encoding Rules (BER).

Common client operations include:

* **StartTLS:** Uses the LDAPv3 TLS extension for secure connections.
* **Bind:** Authenticates the user and specifies the protocol version.
* **Search:** Searches for and retrieves directory entries.
* **Compare:** Checks if a named entry contains a specific attribute value.
* **Add:** Inserts a new entry.
* **Delete:** Removes an entry.
* **Modify:** Changes an existing entry.
* **Modify DN:** Renames or moves an entry.
* **Abandon:** Aborts a previous request.
* **Extended Operation:** A generic operation for defining custom operations.
* **Unbind:** Closes the connection.

LDAP over SSL is also used as a common method for securing communication, typically on port 636. This usage was common in LDAPv2 but was deprecated when LDAPv2 was retired in 2003.

## Directory Structure

LDAP provides an interface for directories following the 1993 X.500 model:

* **Entry:** A set of attributes.
* **Attribute:** Consists of a name (type or description) and one or more values, defined in a schema.
* **Distinguished Name (DN):** A unique identifier for each entry. It consists of the Relative Distinguished Name (RDN)—the filename-like part—followed by the parent's DN (the folder path).
* **UUID:** A unique identifier that may be provided in operational attributes to identify entries reliably even if their DN changes.

**LDIF Example:**

```ldif
dn: cn=John Doe,dc=example,dc=com
cn: John Doe
givenName: John
sn: Doe
telephoneNumber: +1 888 555 6789
telephoneNumber: +1 888 555 1232
mail: john@example.com
manager: cn=Barbara Doe,dc=example,dc=com
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
```

In this example, "dn" is the distinguished name (not an attribute). The other lines represent attributes like common name (cn), domain component (dc), and email address (mail).

Servers hold subtrees starting from a specific entry. If a requested entry is on a different server, the server may return a referral or continuation reference. Some servers also support chaining, where the server contacts the other server and returns results to the client. LDAP generally does not define ordering for values or attributes as they are treated as sets.

## Operations

### Add

Inserts a new entry into the database. If a DN already exists, the server returns decimal code 68 (entryAlreadyExists). Servers do not dereference DNs in add requests; they must conform to naming standards and ensure the immediate superior exists while the entry itself does not.

### Bind (Authenticate)

Sets the authentication state of a session, which is anonymous by default. Simple BIND and SASL PLAIN can send credentials in plaintext and should be encrypted with TLS. SASL provides various mechanisms like Kerberos or client certificates. Successful BIND requests change the authentication state, while unsuccessful ones reset it to anonymous.

### Delete

Transmits a request to remove an entry. Only leaf entries (those with no subordinates) may be deleted via a standard delete request, unless the server supports specific subtree delete controls.

### Search and Compare

The **Search** operation uses several parameters:

* **baseObject:** The starting point for the search.
* **scope:** Defines the depth (BaseObject, singleLevel, or wholeSubtree).
* **filter:** Criteria used to select elements (e.g., `(&(objectClass=person)(|(givenName=John)(mail=john*)))`).
* **derefAliases:** How to follow alias entries.
* **attributes:** The specific attributes to return.
* **sizeLimit/timeLimit:** Constraints on results and execution time.
* **typesOnly:** Returns only attribute types, not values.

The **Compare** operation checks if a specific DN contains an attribute with a precise value.

### Modify

Requests changes to existing entries (add, delete, or replace values). It also includes a Modify-Increment extension for incrementing values by a specified amount. In replicated topologies, clients should use the "post-read control" to verify updates rather than issuing a new search.

### Modify DN

Used to move or rename an entry by providing a new RDN and optionally a new parent DN. Updates are atomic, meaning other operations see either the old or the new entry, but LDAP does not natively support transactions for multiple operations unless extended.

### Extended Operations

Allows for custom operations. **StartTLS** is the most significant, providing confidentiality and integrity by establishing a TLS connection. In contrast, LDAPS establishes TLS immediately upon connection before any LDAP messages are transferred.

### Abandon

Requests the server to abort an operation identified by a message ID. The server is not required to honor this request.

### Unbind

Abandons outstanding operations and closes the connection. While clients can simply close the connection, using Unbind allows the server to gracefully free resources and cancel pending operations.

## URI Scheme

The LDAP URI scheme is structured as follows:
`ldap://host:port/DN?attributes?scope?filter?extensions`

* **host:** FQDN or IP address of the server.
* **port:** Network port (default 389).
* **DN:** Search base distinguished name.
* **attributes:** Comma-separated list of attributes to retrieve.
* **scope:** Search scope (base, one, or sub).
* **filter:** Search filter (e.g., `(objectClass=*)`).

## Schema

The directory schema governs the structure of the Directory Information Tree (DIT). It defines:

* **Attribute Syntaxes:** The kind of information that can be stored.
* **Matching Rules:** How to compare attribute values.
* **Attribute Types:** Unique identifiers (OIDs) and names associated with a syntax and matching rules.
* **Object Classes:** Collections of attributes (e.g., "person" requires "sn" and "cn").
* **Name Forms:** Rules for RDN set requirements.
* **Content Rules:** Constraints on specific object classes or attributes.
* **Structure Rule:** Rules governing types of subordinate entries.

## Security Vulnerabilities

### LDAP Injection

Similar to SQL injection, this occurs when an application fails to sanitize user input in an LDAP query. For example, a malicious user might use a wildcard character (`*`) to match objects they are not authorized to see. Mitigation involves escaping variables using specific encoding functions for DNs and search strings.

### Man-in-the-middle Attacks

Because LDAP was originally created without encryption, attackers can intercept credentials during the bind process. This is mitigated by requiring LDAPS or StartTLS for all binds involving credentials.

## Variations

Server implementations vary greatly; data may be stored in flat files, databases, or served via a gateway. While access control is not standardized, commonly used models exist. LDAP's extensibility allows for new operations, controls, and modified semantics for attributes.

## Other Data Models

Vendors often use LDAP as an access protocol for other services, recasting the data to mimic the LDAP/X.500 model. Examples include accessing SQL databases through LDAP or storing Unix user/group information in LDAP (accessed via PAM and NSS modules). In Active Directory, Kerberos is used for authentication, while LDAP is used for authorization. The GLUE Schema is another example of an LDAP-based data model used in distributed information systems.

## Usage

LDAP servers can return referrals to other servers for requests they cannot fulfill. Organizations often use naming structures like the Country-based model (e.g., `c=US`) or the Domain Component model (e.g., `dc=example,dc=org`). The latter is commonly used with FQDNs (e.g., `ldap://ldap.example.org/dc=example,dc=org`).
