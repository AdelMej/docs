# Dynamic Host Configuration Protocol

The Dynamic Host Configuration Protocol (DHCP) is a network management protocol used on Internet Protocol (IP) networks for automatically assigning IP addresses and other communication parameters to devices connected to the network using a client–server architecture.

The technology eliminates the need for individually configuring network devices manually and consists of two network components: a centrally installed network DHCP server and client instances of the protocol stack on each computer or device. When connected to the network, and periodically thereafter, a client requests a set of parameters from the server using DHCP.

DHCP can be implemented on networks ranging in size from residential networks to large campus networks and regional ISP networks. Many routers and residential gateways have DHCP server capability. Within a local network, a DHCP server assigns a local IP address to each device.

DHCP services exist for networks running Internet Protocol version 4 (IPv4), as well as version 6 (IPv6). The IPv6 version of the DHCP protocol is commonly called DHCPv6.

## History

The Reverse Address Resolution Protocol (RARP) was defined in 1984 for the configuration of simple devices, such as diskless workstations, with a suitable IP address. Acting in the data link layer, it made implementation difficult on many server platforms because it required that a server be present on each individual network link. RARP was superseded by the Bootstrap Protocol (BOOTP) defined in September 1985. This introduced the concept of a relay agent, which allowed the forwarding of BOOTP packets across networks, allowing one central BOOTP server to serve hosts on many IP subnets.

DHCP was first defined in October 1993. It is based on BOOTP but can dynamically allocate IP addresses from a pool and reclaim them when they are no longer in use. It can also be used to deliver a wide range of extra configuration parameters to IP clients, including platform-specific parameters.

Four years later, the DHCPINFORM message type (used for WPAD) and other small changes were added. This definition, from 1997, remains the core of the standard for IPv4 networks.

DHCPv6 was initially defined in 2003. After updates by many subsequent RFCs, its definition was replaced in 2018, where prefix delegation and stateless address autoconfiguration were now merged.

## Overview

Internet Protocol (IP) defines how devices communicate within and across local networks on the Internet. A DHCP server can manage IP settings for devices on its local network, e.g., by assigning IP addresses to those devices automatically and dynamically.

DHCP operates based on the client–server model. When a computer or other device connects to a network, the DHCP client software sends a DHCP broadcast query requesting the necessary information. Any DHCP server on the network may service the request. The DHCP server manages a pool of IP addresses and information about client configuration parameters such as default gateway, domain name, the name servers, and time servers. On receiving a DHCP request, the DHCP server may respond with specific information for each client, as previously configured by an administrator, or with a specific address and any other information valid for the entire network and for the time period for which the allocation (lease) is valid. A DHCP client typically queries this information immediately after booting, and periodically thereafter before the expiration of the information. When a DHCP client refreshes an assignment, it initially requests the same parameter values, but the DHCP server may assign a new address based on the assignment policies set by administrators.

On large networks that consist of multiple links, a single DHCP server may service the entire network when aided by DHCP relay agents located on the interconnecting routers. Such agents relay messages between DHCP clients and DHCP servers located on different subnets.

Depending on implementation, the DHCP server may have three methods of allocating IP addresses:

* **Dynamic allocation:** A network administrator reserves a range of IP addresses for DHCP, and each DHCP client on the LAN is configured to request an IP address from the DHCP server during network initialization. The request-and-grant process uses a lease concept with a controllable time period, allowing the DHCP server to reclaim and then reallocate IP addresses that are not renewed.
* **Automatic allocation:** The DHCP server permanently assigns an IP address to a requesting client from a range defined by an administrator. This is like dynamic allocation, but the DHCP server keeps a table of past IP address assignments, so that it can preferentially assign to a client the same IP address that the client previously had.
* **Manual allocation:** Also called static DHCP allocation, fixed address allocation, reservation, and MAC/IP address binding. An administrator maps a unique identifier (a client id or MAC address) for each client to an IP address, which is offered to the requesting client.

DHCP services are used for both IPv4 and IPv6. The details of the protocol for IPv4 and IPv6 differ sufficiently that they may be considered separate protocols. For the IPv6 operation, devices may alternatively use stateless address autoconfiguration.

## Operation

The DHCP employs a connectionless service model, using the User Datagram Protocol (UDP). It is implemented with two UDP port numbers: the server listens on UDP port 67, and the client listens on UDP port 68.

DHCP operations fall into four phases: server discovery, IP lease offer, IP lease request, and IP lease acknowledgement. These stages are often abbreviated as **DORA**.

The DHCP operation begins with clients broadcasting a request. If the client and server are in different Broadcast Domains, a DHCP Helper or DHCP Relay Agent may be used. Clients requesting renewal of an existing lease may communicate directly via UDP unicast, since the client already has an established IP address at that point. Additionally, there is a BROADCAST flag the client can use to indicate in which way (broadcast or unicast) it can receive the DHCPOFFER: 0x8000 for broadcast, 0x0000 for unicast. Usually, the DHCPOFFER is sent through unicast. For those hosts which cannot accept unicast packets before IP addresses are configured, this flag can be used to work around this issue.

### Discovery

The DHCP client broadcasts a DHCPDISCOVER message on the network subnet using the destination address 255.255.255.255 (limited broadcast) or the specific subnet broadcast address (directed broadcast). A DHCP client may also request an IP address in the DHCPDISCOVER, which the server may take into account when selecting an address to offer.

### Offer

When a DHCP server receives a DHCPDISCOVER message from a client, which is an IP address lease request, the DHCP server reserves an IP address for the client and makes a lease offer by sending a DHCPOFFER message to the client. This message may contain the client's Client ID (Option 61), the IP address that the server is offering, the subnet mask, the lease duration, and the IP address of the DHCP server making the offer. The DHCP server determines the configuration based on the client's hardware address as specified in the CHADDR field.

### Request

In response to the DHCP offer, the client replies with a DHCPREQUEST message, broadcast to the server, requesting the offered address. A client can receive DHCP offers from multiple servers, but it will accept only one DHCP offer. The client must send the server identification option in the DHCPREQUEST message, indicating the server whose offer the client has selected. When other DHCP servers receive this message, they withdraw any offers that they have made to the client and return their offered IP address to the pool of available addresses.

### Acknowledgement

When the DHCP server receives the DHCPREQUEST message from the client, the configuration process enters its final phase. The acknowledgement phase involves sending a DHCPACK packet to the client. This packet includes the lease duration and any other configuration information that the client might have requested. At this point, the IP configuration process is completed.

## Selecting and configuring IP addresses

When the server is reusing an IP address from its pool, it may first check (using ping) to see if it is not taken already. This may happen if a host is configured manually with an IP address that lies within the DHCP scope.

Before claiming an IP address, the client should probe the newly received address (e.g. with ARP), in order to find if there is another host present in the network with the proposed IP address. If there is no reply, this address does not conflict with that of another host, so it is free to be used. If this probe finds another computer using that address, the client should broadcast a DHCPDECLINE to the DHCP server(s).

## Information

A DHCP client may request more information than the server sent with the original DHCPOFFER. The client may also request repeat data for a particular application. For example, browsers use DHCP Inform to obtain web proxy settings via WPAD.

## Releasing

The client sends a request to the DHCP server to release the DHCP information and the client deactivates its IP address. As client devices usually do not know when users may unplug them from the network, the protocol does not mandate the sending of DHCP Release.

## Client configuration parameters

A DHCP server can provide optional configuration parameters to the client. RFC 2132 describes the available DHCP options defined by the Internet Assigned Numbers Authority (IANA). A DHCP client can select, manipulate and overwrite parameters provided by a DHCP server.

## Options

Options are octet strings of varying length using Type–length–value encoding. The first octet is the option code, the second octet is the number of following octets and the remaining octets are code dependent.

### Vendor Extensions (RFC 1497)

| Code | Name | Length | Notes |
| :--- | :--- | :--- | :--- |
| 0 | Pad | 0 octets | Can be used to pad other options so that they are aligned to the word boundary |
| 1 | Subnet mask | 4 octets | Client's subnet mask as per RFC 950. Must be first if router option is included. |
| 2 | Time offset | 4 octets | Time offset of the client's subnet in seconds from UTC. |
| 3 | Router | Multiples of 4 octets | Available routers, listed in order of preference. |
| 4 | Time server | Multiples of 4 octets | Available Time Protocol servers to synchronise with. |
| 5 | Name server | Multiples of 4 octets | Available IEN 116 name servers. |
| 6 | Domain name server | Multiples of 4 octets | Available DNS servers. |
| 7 | Log server | Multiples of 4 octets | Available log servers. |
| 8 | Cookie server | Multiples of 4 octets | Fortune cookie or quote of the day. |
| 9 | LPR Server | Multiples of 4 octets | List of Line Printer Daemon protocol servers. |
| 10 | Impress server | Multiples of 4 octets | List of Imagen Impress servers. |
| 11 | Resource location server | Multiples of 4 octets | List of Resource Location Protocol servers. |
| 12 | Host name | Minimum of 1 octet | Name of the client. |
| 13 | Boot file size | 2 octets | Length of the boot image in 512B blocks. |
| 14 | Merit dump file | Minimum of 1 octet | Path where crash dumps should be stored. |
| 15 | Domain name | Minimum of 1 octet | |
| 16 | Swap server | 4 octets | IP address of a server where a swap service is provided. |
| 17 | Root path | Minimum of 1 octet | Path in the remote filesystem to mount as root. |
| 18 | Extensions path | Minimum of 1 octet | |
| 255 | End | 0 octets | Used to mark the end of the vendor option field. |

### IP layer parameters per host

| Code | Name | Length | Notes |
| :--- | :--- | :--- | :--- |
| 19 | IP forwarding enable/disable | 1 octet | |
| 20 | Non-local source routing enable/disable | 1 octet | |
| 21 | Policy filter | Multiples of 8 octets | |
| 22 | Maximum datagram reassembly size | 2 octets | |
| 23 | Default IP time-to-live | 1 octet | |
| 24 | Path MTU aging timeout | 4 octets | |
| 25 | Path MTU plateau table | Multiples of 2 octets | |

### IP Layer Parameters per Interface

| Code | Name | Length | Notes |
| :--- | :--- | :--- | :--- |
| 26 | Interface MTU | 2 octets | |
| 27 | All subnets are local | 1 octet | |
| 28 | Broadcast address | 4 octets | |
| 29 | Perform mask discovery | 1 octet | |
| 30 | Mask supplier | 1 octet | |
| 31 | Perform router discovery | 1 octet | |
| 32 | Router solicitation address | 4 octets | |
| 33 | Static route | Multiples of 8 octets | List of destination/router pairs. |

### Link layer parameters per interface

| Code | Name | Length | Notes |
| :--- | :--- | :--- | :--- |
| 34 | Trailer encapsulation option | 1 octet | |
| 35 | ARP cache timeout | 4 octets | |
| 36 | Ethernet encapsulation | 1 octet | |

### TCP parameters

| Code | Name | Length | Notes |
| :--- | :--- | :--- | :--- |
| 37 | TCP default TTL | 1 octet | |
| 38 | TCP keepalive interval | 4 octets | |
| 39 | TCP keepalive garbage | 1 octet | |

### Application and service parameters

| Code | Name | Length | Notes |
| :--- | :--- | :--- | :--- |
| 40 | Network information service domain | Minimum of 1 octet | |
| 41 | Network information servers | Multiples of 4 octets | |
| 42 | Network Time Protocol (NTP) servers | Multiples of 4 octets | |
| 43 | Vendor-specific information | Minimum of 1 octets | |
| 44 | NetBIOS over TCP/IP name server | Multiples of 4 octets | |
| 45 | NetBIOS over TCP/IP datagram Distribution Server | Multiples of 4 octets | |
| 46 | NetBIOS over TCP/IP node type | 1 octet | |
| 47 | NetBIOS over TCP/IP scope | Minimum of 1 octet | |
| 48 | X Window System font server | Multiples of 4 octets | |
| 49 | X Window System display manager | Multiples of 4 octets | |
| 64 | Network Information Service+ domain | Minimum of 1 octet | |
| 65 | Network Information Service+ servers | Multiples of 4 octets | |
| 68 | Mobile IP home agent | Multiples of 4 octets | |
| 69 | Simple Mail Transfer Protocol (SMTP) server | Multiples of 4 octets | |
| 70 | Post Office Protocol (POP3) server | Multiples of 4 octets | |
| 71 | Network News Transfer Protocol (NNTP) server | Multiples of 4 octets | |
| 72 | Default World Wide Web (WWW) server | Multiples of 4 octets | |
| 73 | Default Finger protocol server | Multiples of 4 octets | |
| 74 | Default Internet Relay Chat (IRC) server | Multiples of 4 octets | |
| 75 | StreetTalk server | Multiples of 4 octets | |
| 76 | StreetTalk Directory Assistance (STDA) server | Multiples of 4 octets | |

### DHCP extensions

| Code | Name | Length | Notes |
| :--- | :--- | :--- | :--- |
| 50 | Requested IP address | 4 octets | |
| 51 | IP address lease time | 4 octets | |
| 52 | Option overload | 1 octet | |
| 53 | DHCP message type | 1 octet | |
| 54 | Server identifier | 4 octets | |
| 55 | Parameter request list | Minimum of 1 octet | |
| 56 | Message | Minimum of 1 octet | |
| 57 | Maximum DHCP message size | 2 octets | |
| 58 | Renewal (T1) time value | 4 octets | |
| 59 | Rebinding (T2) time value | 4 octets | |
| 60 | Vendor class identifier | Minimum of 1 octet | Used to identify vendor/functionality. |
| 61 | Client identifier | Minimum of 2 octets | |
| 66 | TFTP server name | Minimum of 1 octet | |
| 67 | Bootfile name | Minimum of 1 octet | |

## DHCP message types

| Code | Name | Length | RFC |
| :--- | :--- | :--- | :--- |
| 1 | DHCPDISCOVER | 1 octet | 2132 |
| 2 | DHCPOFFER | 1 octet | 2132 |
| 3 | DHCPREQUEST | 1 octet | 2132 |
| 4 | DHCPDECLINE | 1 octet | 2132 |
| 5 | DHCPACK | 1 octet | 2132 |
| 6 | DHCPNAK | 1 octet | 2132 |
| 7 | DHCPRELEASE | 1 octet | 2132 |
| 8 | DHCPINFORM | 1 octet | 2132 |
| 9 | DHCPFORCERENEW | 1 octet | 3203 |
| 10 | DHCPLEASEQUERY | 1 octet | 4388 |
| 11 | DHCPLEASEUNASSIGNED | 1 octet | 4388 |
| 12 | DHCPLEASEUNKNOWN | 1 octet | 4388 |
| 13 | DHCPLEASEACTIVE | 1 octet | 4388 |
| 14 | DHCPBULKLEASEQUERY | 1 octet | 6926 |
| 15 | DHCPLEASEQUERYDONE | 1 octet | 6926 |
| 16 | DHCPACTIVELEASEQUERY | 1 octet | 7724 |
| 17 | DHCPLEASEQUERYSTATUS | 1 octet | 7724 |
| 18 | DHCPTLS | 1 octet | 7724 |

## Client vendor identification

An option exists to identify the vendor and functionality of a DHCP client. This is often done using the Vendor Class Identifier (VCI) (Option 60). The value set in this option gives the DHCP server a hint about required extra information. For example, an Aruba campus wireless access point supplies 'ArubaAP' as option 60, allowing the server to augment its DHCPOFFER with an IP address of an Aruba wireless controller in option 43.

## Other extensions

| Code | Name | Length | RFC |
| :--- | :--- | :--- | :--- |
| 77 | User Class | Minimum of 2 octets | 3004 |
| 82 | Relay agent information | Minimum of 2 octets | 3046 |
| 85 | Novell Directory Service (NDS) servers | Min 4 octets | 2241 |
| 86 | NDS tree name | Variable | 2241 |
| 87 | NDS context | Variable | 2241 |
| 100 | Time zone, POSIX style | Variable | 4833 |
| 101 | Time zone, tz database style | Variable | 4833 |
| 114 | DHCP Captive Portal | Variable | 8910 |
| 119 | Domain search | Variable | 3397 |
| 121 | Classless static route | Variable | 3442 |
| 209 | Configuration File | Variable | 5071 |
| 210 | Path Prefix | Variable | 5071 |
| 211 | Reboot Time | Variable | 5071 |
| 224–254 | site-specific options | Variable | 3942 |

### Relay agent information sub-options (Option 82)

| Code | Name | Length | RFC |
| :--- | :--- | :--- | :--- |
| 1 | Agent Circuit ID | Minimum of 1 octet | 3046 |
| 2 | Agent Remote ID | Minimum of 1 octet | 3046 |
| 4 | DOCSIS device class | 4 octets | 3256 |
| 5 | Link Selection | 4 octets | 3527 |

## Relaying

In networks with multiple subnets, a DHCP client on a subnet without a server cannot communicate directly with the server because its broadcast is limited to its own subnet. DHCP relay agents are installed on routers to route these messages. The relay agent receives the broadcast and transmits it to one or more servers using unicast.

The relay agent stores its IP address from the interface where it received the broadcast in the GIADDR field of the DHCP packet. The server uses this GIADDR to determine the correct subnet and address pool. When replying, the server sends the response to the GIADDR address via unicast; the relay agent then retransmits it on the local network as a unicast (or broadcast if requested) to the client's MAC address.

## Client states

A DHCP client moves through states based on how the server responds to its messages:

* **DHCPOFFER**
* **DHCPACK**
* **DHCPNAK**

## Reliability

DHCP ensures reliability via periodic renewal, rebinding, and failover. Clients are allocated leases and begin attempting to renew them once half the lease interval has expired by sending a unicast DHCPREQUEST. If the server is unreachable, the client repeats this request periodically.

If the server remains unreachable for an extended period, the client attempts to rebind by broadcasting its DHCPREQUEST to reach all available servers. If rebinding fails, the lease expires, and the client must stop using the IP address and restart the DHCP process from the beginning (DHCPDISCOVER).

## IPv6 networks

DHCP has been used for assigning parameters in IPv6 networks despite IPv6's inherent features for stateless address autoconfiguration. The IPv6 version is designated as DHCPv6.

## Security

Base DHCP does not include any mechanism for authentication and is vulnerable to:

1. **Unauthorized DHCP servers:** Providing false information (rogue DHCP) can lead to denial-of-service or man-in-the-middle attacks.
2. **Unauthorized clients:** Clients can gain unauthorized access by presenting credentials belonging to other clients.
3. **Resource exhaustion:** Malicious clients can exhaust the server's IP address store.

Mitigation strategies include:

* **Option 82 (Relay Agent Information):** Allows operators to attach tags to messages on a trusted network, using them as authorization tokens.
* **Authentication for DHCP Messages (RFC 3118):** Provides a mechanism for authenticating DHCP messages, though it has seen limited deployment due to key management challenges.
* **Architectural proposals:** Include authenticating requests using 802.1X or PANA.
