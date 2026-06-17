# Domain Name System

The Domain Name System (DNS) is a hierarchical and distributed name service that provides a naming system for computers, services, and other resources on the Internet or other Internet Protocol (IP) networks. It associates various information with domain names (identification strings) assigned to each of the associated entities. Most prominently, it translates readily memorized domain names to the numerical IP addresses needed for locating and identifying computer services and devices with the underlying network protocols. The Domain Name System has been an essential component of the functionality of the Internet since 1985.

## Overview

* **Purpose**: To identify resources on a network.
* **Developer**: Paul Mockapetris.
* **Introduction**: November 1983.
* **OSI Layer**: Application layer.
* **Port**: 53 (UDP/TCP).

The Domain Name System delegates the responsibility of assigning domain names and mapping those names to Internet resources by designating authoritative name servers for each domain. Network administrators may delegate authority over subdomains of their allocated name space to other name servers. This mechanism provides distributed and fault-tolerant service and was designed to avoid a single large central database.

In addition, the DNS specifies the technical functionality of the database service that is at its core. It defines the DNS protocol, a detailed specification of the data structures and data communication exchanges used in the DNS, as part of the Internet protocol suite.

The Internet maintains two principal namespaces: the domain name hierarchy and the IP address spaces. The Domain Name System maintains the domain name hierarchy and provides translation services between it and the address spaces. Internet name servers and a communication protocol implement the Domain Name System. A DNS name server is a server that stores the DNS records for a domain; a DNS name server responds with answers to queries against its database.

The most common types of records stored in the DNS database are for start of authority (SOA), IP addresses (A and AAAA), SMTP mail exchangers (MX), name servers (NS), pointers for reverse DNS lookups (PTR), and domain name aliases (CNAME). Although not intended to be a general-purpose database, DNS has been expanded over time to store records for other types of data for either automatic lookups, such as DNSSEC records, or for human queries such as responsible person (RP) records. As a general-purpose database, the DNS has also been used in combating unsolicited email (spam) by storing blocklists. The DNS database is conventionally stored in a structured text file, the zone file, but other database systems are common.

The Domain Name System originally used the User Datagram Protocol (UDP) as transport over IP. Reliability, security, and privacy concerns spawned the use of the Transmission Control Protocol (TCP) as well as numerous other protocol developments.

## Function

An often-used analogy to explain the DNS is that it serves as the phone book for the Internet by translating human-friendly computer hostnames into IP addresses. For example, the hostname <www.example.com> within the domain name example.com translates to the addresses 93.184.216.34 (IPv4) and 2606:2800:220:1:248:1893:25c8:1946 (IPv6). The DNS can be quickly and transparently updated, allowing a service's location on the network to change without affecting the end users, who continue to use the same hostname.

An important and ubiquitous function of the DNS is its central role in distributed Internet services such as cloud services and content delivery networks. When a user accesses a distributed Internet service using a URL, the domain name of the URL is translated to the IP address of a server that is proximal to the user. The key functionality of the DNS exploited here is that different users can simultaneously receive different translations for the same domain name, a key point of divergence from a traditional phone-book view of the DNS. This process of using the DNS to assign proximal servers to users is key to providing faster and more reliable responses on the Internet and is widely used by most major Internet services.

The DNS reflects the structure of administrative responsibility on the Internet. Each subdomain is a zone of administrative autonomy delegated to a manager. For zones operated by a registry, administrative information is often complemented by the registry's RDAP and WHOIS services. That data can be used to gain insight on, and track responsibility for, a given host on the Internet.

## History

Using a simpler, more memorable name in place of a host's numerical address dates back to the ARPANET era. The Stanford Research Institute (now SRI International) maintained a text file named HOSTS.TXT that mapped host names to the numerical addresses of computers on the ARPANET. Elizabeth Feinler developed and maintained the first ARPANET directory. Maintenance of numerical addresses, called the Assigned Numbers List, was handled by Jon Postel at the University of Southern California's Information Sciences Institute (ISI), whose team worked closely with SRI.

Addresses were assigned manually. Computers, including their hostnames and addresses, were added to the primary file by contacting the SRI Network Information Center (NIC), directed by Feinler, via telephone during business hours. Later, Feinler set up a WHOIS directory on a server in the NIC for retrieval of information about resources, contacts, and entities. She and her team developed the concept of domains. Feinler suggested that domains should be based on the location of the physical address of the computer. Computers at educational institutions would have the domain edu, for example. She and her team managed the Host Naming Registry from 1972 to 1989.

By the early 1980s, maintaining a single, centralized host table had become slow and unwieldy and the emerging network required an automated naming system to address technical and personnel issues. Postel directed the task of forging a compromise between five competing proposals of solutions to Paul Mockapetris. Mockapetris instead created the Domain Name System in 1983 while at the University of Southern California.

The Internet Engineering Task Force published the original specifications in RFC 882 and RFC 883 in November 1983. These were updated in RFC 973 in January 1986.

In 1984, four UC Berkeley students, Douglas Terry, Mark Painter, David Riggle, and Songnian Zhou, wrote the first Unix name server implementation for the Berkeley Internet Name Domain, commonly referred to as BIND. In 1985, Kevin Dunlap of DEC substantially revised the DNS implementation. Mike Karels, Phil Almquist, and Paul Vixie then took over BIND maintenance. Internet Systems Consortium was founded in 1994 by Rick Adams, Paul Vixie, and Carl Malamud, expressly to provide a home for BIND development and maintenance. BIND versions from 4.9.3 onward were developed and maintained by ISC, with support provided by ISC's sponsors. As co-architects/programmers, Bob Halley and Paul Vixie released the first production-ready version of BIND version 8 in May 1997. Since 2000, over 43 different core developers have worked on BIND.

In November 1987, RFC 1034 and RFC 1035 superseded the 1983 DNS specifications. Several additional Request for Comments have proposed extensions to the core DNS protocols.

## Structure

### Domain Name Space

The domain name space consists of a tree data structure. Each node or leaf in the tree has a label and zero or more resource records (RR), which hold information associated with the domain name. The domain name itself consists of the label, concatenated with the name of its parent node on the right, separated by a dot.

The tree sub-divides into zones beginning at the root zone. A DNS zone may consist of as many domains and subdomains as the zone manager chooses. DNS can also be partitioned according to class where the separate classes can be thought of as an array of parallel namespace trees.

The hierarchical Domain Name System for class Internet, organized into zones, each served by a name server:

* Administrative responsibility for any zone may be divided by creating additional zones.
* Authority over the new zone is said to be delegated to a designated name server.
* The parent zone ceases to be authoritative for the new zone.

### Domain Name Syntax, Internationalization

The definitive descriptions of the rules for forming domain names appear in RFC 1035, RFC 1123, RFC 2181, and RFC 5892. A domain name consists of one or more parts, technically called labels, that are conventionally concatenated, and delimited by dots, such as example.com.

* The right-most label conveys the top-level domain (e.g., .com).
* The hierarchy descends from right to left; each label to the left specifies a subdivision of the domain to the right.
* This tree of subdivisions may have up to 127 levels.
* A label may contain zero to 63 characters.
* The null label of length zero is reserved for the root zone.
* The full domain name may not exceed 253 characters in its textual representation (or 254 with the trailing dot).
* In the internal binary representation, this requires 255 octets of storage.

While no technical limitation exists to prevent labels from using any character representable by an octet, hostnames use a preferred format: the LDH rule (letters, digits, hyphen). Domain names are interpreted in a case-independent manner. Labels may not start or end with a hyphen. Top-level domain names should not be all-numeric.

To support multiple languages, ICANN approved the Internationalizing Domain Names in Applications (IDNA) system, which maps Unicode strings into the valid DNS character set using Punycode.

### Name Servers

The Domain Name System is maintained by a distributed database system, which uses the client-server model. The nodes of this database are the name servers. Each domain has at least one authoritative DNS server that publishes information about that domain and the name servers of any domains subordinate to it. The top of the hierarchy is served by the root name servers, the servers to query when looking up (resolving) a TLD.

#### Authoritative Name Server

An authoritative name server only gives answers to DNS queries from data that have been configured by an original source.

* **Primary Server**: Stores the original copies of all zone records.
* **Secondary Server**: Uses a special automatic updating mechanism in communication with its primary to maintain an identical copy of the primary records.

Every DNS zone must be assigned a set of authoritative name servers, stored in the parent domain zone with NS records. An authoritative server indicates its status by setting a protocol flag called the "Authoritative Answer" (AA) bit in its responses. When a name server is designated as authoritative for a domain it does not have data for, it presents a "lame delegation" or "lame response."

## Operation

### Address Resolution Mechanism

Domain name resolvers determine the responsible name servers by a sequence of queries starting with the right-most (top-level) domain label. A resolver typically starts with a query to one of the root servers. Root servers usually respond with a referral to more authoritative servers (e.g., .org). The resolver iteratively repeats this process until it receives an authoritative answer. Caching is used in DNS servers to off-load the root servers.

### Recursive and Caching Name Server

While authoritative name servers are sufficient in theory, every query would have to start at the root zone and users would need to implement recursive software. To improve efficiency, DNS supports cache servers which store results for a period determined by the time-to-live (TTL). Typically, caching DNS servers also implement the recursive algorithm necessary to resolve a given name starting with the DNS root through to the authoritative name servers of the queried domain.

### DNS Resolvers

The client side is called a DNS resolver, responsible for initiating and sequencing queries. They are classified by query methods:

* **Non-recursive query**: The server provides a record it is authoritative for or a partial result without querying other servers.
* **Recursive query**: The DNS server answers the query completely by querying other name servers as needed. A client typically issues a recursive query to a caching recursive DNS server.
* **Iterative query**: The resolver queries a chain of servers; each refers the client to the next server until the request is resolved.

### Circular Dependencies and Glue Records

Name servers in delegations are identified by name, not IP address. If a name server is a subdomain of the domain for which it provides delegation, a circular dependency occurs. In this case, the delegating name server must provide "glue"—IP addresses for the authoritative name server mentioned in the delegation.

### Record Caching

DNS query results are cached locally or on intermediary hosts. Each result comes with a TTL, indicating how long information remains valid before it is discarded or refreshed. TTLs can range from seconds to weeks. Changes do not propagate immediately but require caches to expire and refresh after the TTL.

### Reverse Lookup

A reverse DNS lookup queries for domain names when an IP address is known. The DNS stores these in pointer (PTR) records within the `in-addr.arpa` (IPv4) or `ip6.arpa` (IPv6) infrastructure top-level domains.

### Client Lookup

Users generally do not communicate directly with a DNS resolver; this takes place transparently in applications like web browsers or email clients. The application sends a request to the local operating system's resolver, which handles the communication. If the cache has the answer, it is returned immediately; otherwise, the resolver queries designated DNS servers and caches the result for future use.

### Broken Resolvers

Some large ISPs may violate rules, such as disobeying TTLs or indicating a domain doesn't exist because a specific name server is unresponsive. Some applications maintain internal DNS caches to avoid network lookups, which can complicate debugging.

## Other Applications

* **Virtual Hosting**: Multiple hostnames can correspond to a single IP address.
* **Load Distribution**: A single hostname can resolve to many IP addresses.
* **Mail Transfer**: MX records map a domain to a mail exchanger.
* **Blocklisting**: DNS is used for efficient storage and distribution of IP addresses of block-listed email hosts.
* **Dynamic DNS (DDNS)**: Updates a DNS server with a client IP address on-the-fly as it changes.

## DNS Message Format

DNS uses two types of messages: queries and responses; both share the same format consisting of a header and four sections: question, answer, authority, and additional space.

### Header Fields

The header consists of 16-bit fields:

* **Identification**: Matches responses with queries.
* **Flags**:
  * QR: Query (0) or Reply (1).
  * OPCODE: Query (0), IQUERY (1), or Status (2).
  * AA: Authoritative Answer.
  * TC: Truncation.
  * RD: Recursion Desired.
  * RA: Recursion Available.
  * Z: Reserved (0).
  * AD: Authentic Data.
  * CD: Checking Disabled.
  * RCODE: Response code (e.g., NOERROR, NXDOMAIN).
* **Number of Questions**
* **Number of Answers**
* **Number of Authority RRs**
* **Number of Additional RRs**

### Question Section

Contains the requested resource name, Type (e.g., A, MX), and Class code.

### Resource Records (RR)

The database of information elements. Each record has a type, TTL, class, and type-specific data (RDATA).

* **NAME**: Fully qualified domain name.
* **TYPE**: Indicates the format and use (e.g., A for IPv4, NS for name servers).
* **CLASS**: Usually "IN" (Internet).
* **TTL**: Seconds the RR stays valid.
* **RDLENGTH**: Length of RDATA field.
* **RDATA**: Data specific to the record type.

### Wildcard Records

Specify names that start with an asterisk (*) label. They allow for generating resource records by substituting whole labels with matching components of the query name.

## Protocol Extensions and Transport

### Extension Mechanisms for DNS (EDNS)

Introduced optional protocol elements without increasing overhead when not in use, such as increasing the DNS message size in UDP datagrams.

### Dynamic Zone Updates

Uses the UPDATE DNS opcode to add or remove records dynamically from an authoritative server's database.

### Transport Protocols

* **UDP/TCP Port 53**: The conventional transport. UDP is used for single-packet requests; TCP allows for longer responses and reliable delivery.
* **DNS over TLS (DoT)**: Encrypts the entire connection using TLS over TCP port 853.
* **DNS over HTTPS (DoH)**: Tunnels DNS query data over HTTPS (TCP port 443).
* **DNS over QUIC (DoQ)**: Provides privacy properties similar to DoT with latency characteristics similar to UDP.
* **DNS over CoAP (DoC)**: Aimed at the constrained Internet of Things (IoT).
* **Oblivious DoH (ODoH)**: Combines ingress/egress separation with DoH's HTTPS tunneling and TLS encryption.
* **DNS over Tor**: Runs DNS over the Tor network to hide both name resolution and user traffic.
* **DNSCrypt**: Encrypts query payloads using servers' public keys. It supports an "anonymized" mode similar to Oblivious DNS.

## Security Issues

The expansion of the Internet into the commercial sector necessitated security measures to protect data integrity and user authentication.

* **DNS Cache Poisoning**: Distributing false information to caching resolvers.
* **DNSSEC**: Adds support for cryptographically signed responses.
* **TSIG**: Provides cryptographic authentication between trusted peers for zone transfers or dynamic updates.
* **DNS Spoofing (IDN Homograph Attack)**: Exploiting similar-looking characters in internationalized domain names to mimic websites like paypal.com.
* **DNSMessenger**: A technique using DNS TXT records to communicate and control malware remotely.

## Privacy and Tracking Issues

The DNS protocol lacks confidentiality controls, meaning queries are often sent unencrypted, making them vulnerable to sniffing, hijacking, and censorship.

* **Solutions**: VPNs, Tor, Proxies, and public DNS servers supporting DoH, DoT, or DNSCrypt.
* **Centralization**: Critics note that a few large companies providing public DNS resolvers may contribute to the centralization of the Internet.

## Domain Name Registration

The right to use a domain name is delegated by registrars accredited by ICANN or other organizations.

* **Registry**: Operates the database of names within an authoritative zone.
* **Registrar**: Authorized to assign names in a corresponding zone and publish information via WHOIS.
* **Registrant**: The person or organization who asked for domain registration.
* **RDAP**: A replacement for WHOIS being considered for data access.
