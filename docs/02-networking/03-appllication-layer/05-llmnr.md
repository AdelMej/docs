# Link-Local Multicast Name Resolution

The Link-Local Multicast Name Resolution (LLMNR) is a protocol based on the Domain Name System (DNS) packet format that allows both IPv4 and IPv6 hosts to perform name resolution for hosts on the same local link. It is included in Windows Vista, Windows Server 2008, Windows 7, Windows 8, and Windows 10. It is also implemented by systemd-resolved on Linux. LLMNR is defined in RFC 4795 but was not adopted as an IETF standard.

As of April 2022, Microsoft has begun the process of phasing out both LLMNR and NetBIOS name resolution in favour of mDNS.

## Protocol details
In responding to queries, responders listen on UDP port 5355 on the following link-scope Multicast address:

*   **IPv4:** 224.0.0.252, MAC address 01-00-5E-00-00-FC
*   **IPv6:** FF02:0:0:0:0:0:1:3 (this notation can be abbreviated as FF02::1:3), MAC address 33-33-00-01-00-03

The responders also listen on TCP port 5355 on the unicast address that the host uses to respond to queries.

## Packet header structure

| Bit offset | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **0** | ID | | | | | | | | | | | | | | | |
| **16** | QR | Opcode | C | TC | T | Z | Z | Z | Z | RCODE | | | | | | |
| **32** | QDCOUNT | | | | | | | | | | | | | | | |
| **48** | ANCOUNT | | | | | | | | | | | | | | | |
| **64** | NSCOUNT | | | | | | | | | | | | | | | |
| **80** | ARCOUNT | | | | | | | | | | | | | | | |

*   **ID** - A 16-bit identifier assigned by the program that generates any kind of query.
*   **QR** - Query/Response.
*   **OPCODE** - A 4-bit field that specifies the kind of query in this message. This value is set by the originator of a query and copied into the response. This specification defines the behavior of standard queries and responses (opcode value of zero). Future specifications may define the use of other opcodes with LLMNR.
*   **C** - Conflict.
*   **TC** - TrunCation.
*   **T** - Tentative.
*   **Z** - Reserved for future use.
*   **RCODE** - Response code.
*   **QDCOUNT** - An unsigned 16-bit integer specifying the number of entries in the question section.
*   **ANCOUNT** - An unsigned 16-bit integer specifying the number of resource records in the answer section.
*   **NSCOUNT** - An unsigned 16-bit integer specifying the number of name server resource records in the authority records section.
*   **ARCOUNT** - An unsigned 16-bit integer specifying the number of resource records in the additional records section.
