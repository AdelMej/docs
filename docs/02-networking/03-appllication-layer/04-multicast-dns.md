# Multicast DNS

## Service discovery protocol

Multicast DNS (mDNS) is a computer networking protocol that resolves hostnames to IP addresses within small networks that do not include a local name server. It is a zero-configuration service, using essentially the same programming interfaces, packet formats and operating semantics as unicast Domain Name System (DNS). It was designed to work as either a stand-alone protocol or compatible with standard DNS servers. mDNS uses IP multicast and User Datagram Protocol (UDP) packets and is implemented by Apple Bonjour, Windows 10 and later and open-source Avahi software packages included in most Linux distributions. Although the Windows 10 implementation in earlier releases (Version 1703) was limited to discovering networked printers, screen mirroring devices, wireless speakers etc, subsequent releases (Windows 10 1903 and later) resolved hostnames as well. mDNS can work in conjunction with DNS Service Discovery (DNS-SD), a companion zero-configuration networking technique specified separately in RFC 6763.

## History

Multicast DNS was first proposed by Bill Woodcock and Bill Manning in the IETF in 2000, and was eventually published as standards-track RFC 6762 by Stuart Cheshire and Marc Krochmal thirteen years later.

## Protocol overview

When an mDNS client needs to resolve a hostname, it sends an IP multicast query message that asks the host having that name to identify itself. That target machine then multicasts a message that includes its IP address. All machines in that subnet can then use that information to update their mDNS caches. Any host can relinquish its claim to a name by sending a response packet with a time to live (TTL) equal to zero.

By default, mDNS exclusively resolves hostnames ending with the .local top-level domain. This can cause problems if .local includes hosts that do not implement mDNS but that can be found via a conventional unicast DNS server. Resolving such conflicts requires network-configuration changes that mDNS was designed to avoid.

## Packet structure

An mDNS message is a multicast UDP packet sent using the following addressing:

* IPv4 address 224.0.0.251 or IPv6 address ff02::fb
* UDP port 5353
* When using Ethernet frames, the standard IP multicast MAC address 01:00:5E:00:00:FB (for IPv4) or 33:33:00:00:00:FB (for IPv6)

The payload structure is based on the unicast DNS packet format, consisting of two parts—the header and the data. The header is identical to that found in unicast DNS, as are the sub-sections in the data part: queries, answers, authoritative-nameservers, and additional records. The number of records in each sub-section matches the value of the corresponding *COUNT field in the header.

## Queries

The wire format for records in the query section is slightly modified from that in unicast DNS, adding the single-bit UNICAST-RESPONSE field.

### mDNS Query section fields

| Field | Description | Length bits |
| :--- | :--- | :--- |
| QNAME | Name of the node to which the query pertains | Variable |
| QTYPE | The type of the query, i.e. the type of Resource Record which should be returned in responses. | 16 |
| UNICAST-RESPONSE | Boolean flag indicating whether a unicast-response is desired | 1 |
| QCLASS | Class code, 1 a.k.a. "IN" for the Internet and IP networks | 15 |

As in unicast DNS, the QNAME field consists of a series of length/value sub-fields called labels. Each label represents one of the dot-separated substrings in a fully qualified domain name (FQDN). The list is terminated by either a single null-byte representing the root of the DNS, or by a byte with the two high-order bits set (value 192) to signal an indirect pointer to another location in the message. This is known as name compression in RFC 6762.

The UNICAST-RESPONSE field is used to minimize unnecessary broadcasts on the network: if the bit is set, responders SHOULD send a directed-unicast response directly to the inquiring node rather than broadcasting the response to the entire network.

The QCLASS field is identical to that found in unicast DNS.

## Resource Records

All records in the answers, authoritative-nameservers, and additional records sections have the same format and are collectively known as Resource Records (RR).

Resource Records in mDNS also have a slightly modified general format compared to unicast DNS:

### mDNS Resource Record fields

| Field | Description | Length bits |
| :--- | :--- | :--- |
| RRNAME | Name of the node to which the record pertains | Variable |
| RRTYPE | The type of the Resource Record | 16 |
| CACHE-FLUSH | Boolean flag indicating whether outdated cached records should be purged | 1 |
| RRCLASS | Class code, 1 a.k.a. "IN" for the Internet and IP networks | 15 |
| TTL | Time interval (in seconds) that the RR should be cached | 32 |
| RDLENGTH | Integer representing the length (in octets) of the RDATA field | 16 |
| RDATA | Resource data; internal structure varies by RRTYPE | Variable |

The CACHE-FLUSH bit is used to instruct neighbor nodes that the record should overwrite, rather than be appended onto, any existing cached entries for this RRNAME and RRTYPE.

The formats of the RDATA fields are the same as those found in unicast DNS. However, DNS Service Discovery (DNS-SD), the most common use-case for mDNS, specifies slight modifications to some of their formats (notably TXT records).
