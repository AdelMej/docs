# DHCPv6

The Dynamic Host Configuration Protocol version 6 (DHCPv6) is a network protocol for configuring Internet Protocol version 6 (IPv6) hosts with IP addresses, IP prefixes, and other configuration data required to operate in an IPv6 network. It is not just the IPv6 equivalent of the Dynamic Host Configuration Protocol for IPv4.

## Overview
*   **Purpose**: Provide IPv6 addresses and additional network configuration parameters to clients in an IPv6 network.
*   **Developers**: Michael Carney, Charles E. Perkins, Bernie Volz, Ted Lemon, Jim Bound
*   **Introduction**: June 2003
*   **Based on**: Dynamic Host Configuration Protocol for IPv4
*   **OSI Layer**: Layer 7 Application
*   **Ports**: 
    *   UDP port 546 (Clients)
    *   UDP port 547 (Servers and relay agents)

## Introduction
IPv6 hosts may automatically generate IP addresses internally using stateless address autoconfiguration (SLAAC), or they may be assigned configuration data with DHCPv6, or both. DHCPv6 and SLAAC are complementary services. Unlike the Neighbor Discovery Protocol (NDP) used by SLAAC, DHCPv6 can not only assign single unicast addresses, but also entire prefixes in prefix delegation. For example, an ISP's router can provide a prefix to a customer's router via DHCPv6 so that the customer's router can assign addresses to the customer's many devices via either DHCPv6 or SLAAC. This allows routers for residential networks to be configured with no operator intervention.

DHCPv6 also allows the distribution of information other than what SLAAC/NDP provides on a given network: this works even without DHCPv6 managing the distribution of network addresses. The standard method for a SLAAC/NDP network to hand out Domain Name System (DNS) server settings is via setting a flag in the Router Advertisement (RA) message telling the clients to ask for such settings over DHCPv6, although this specific use case is being replaced via a nonstandard extension of the RA message. Still, there remains a plethora of DHCPv6 options for providing additional information not handled by SLAAC/NDP, much like the wide range of information conveyed by legacy DHCP options.

Finally, DHCPv6 also offers a stateful approach, which provides more control than SLAAC's stateless approach.

## Operation

### Multicast Addresses in DHCPv6
DHCPv6 uses IPv6 multicast addresses to enable communication between clients, relay agents, and servers when unicast addresses are not yet known. RFC 9915 defines two well-known multicast groups for this purpose:

| Multicast Address | Name | Scope | Used by | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| ff02::1:2 | All_DHCP_Relay_Agents_and_Servers | Link-local | Clients | Discover on-link DHCPv6 servers and relay agents |
| ff05::1:3 | All_DHCP_Servers | Site-local | Relay agents | Forward client messages to all DHCPv6 servers within a site |

**Notes:**
*   Link-local multicast traffic (ff02::1:2) is never forwarded beyond the local link.
*   Site-local multicast (ff05::1:3) requires the relay agent to have an address with sufficient scope to reach servers within the site.
*   Multicast support is mandatory for correct DHCPv6 operation.

### Port Numbers
Clients listen for DHCP messages on UDP port 546. Servers and relay agents listen for DHCP messages on UDP port 547.

### Identifiers
#### DHCP Unique Identifier (DUID)
The DHCP unique identifier (DUID) is used by a client to get an IP address from a DHCPv6 server. It has a 2-byte DUID type field, and a variable-length identifier field up to 128 bytes. Its actual length depends on its type. The server compares the DUID with its database and delivers configuration data (address, lease times, DNS servers, etc.) to the client.

Four DUID types are identified:
1.  **DUID-LLT** (link-layer address plus time): Consists of DUID type (1), hardware type, time value, and link-layer address. This is recommended for general-purpose computing devices with writable persistent storage.
2.  **DUID-EN** (Vendor-assigned identifier based on Enterprise Number): Assigned by the device vendor; consists of DUID type (2), Vendor’s IANA-assigned Private Enterprise Number, and a vendor-defined unique identifier.
3.  **DUID-LL** (link-layer address): Consists of DUID type (3), hardware type, and link-layer address. Intended for devices with a permanently attached network interface and no writable persistent storage.
4.  **DUID-UUID** (Universally Unique Identifier): Uses a 128-bit UUID as its identifier (DUID type 4).

#### RFC 6939: Client Link-Layer Address Option
Due to the difficulty of managing multiple identifiers in a dual-stack environment, and because DUIDs are not optimal for some situations, RFC 6939 was released to provide a way to identify a host based on its MAC address. It defines a way for a DHCPv6 relay to pass that information to a DHCPv6 server.

## Example
In a standard sequence (without rapid-commit):
1.  Client sends a solicit from [fe80::aabb:ccff:fedd:eeff]:546 to multicast address [ff02::1:2]:547.
2.  Server replies with an advertise from [fe80::0011:22ff:fe33:5566]:547 to [fe80::aabb:ccff:fedd:eeff]:546.
3.  Client replies with a request from [fe80::aabb:ccff:fedd:eeff]:546 to [ff02::1:2]:547.
4.  Server finishes with a reply from [fe80::0011:22ff:fe33:5566]:547 to [fe80::aabb:ccff:fedd:eeff]:546.

## Client/Server Message Formats
DHCP messages utilize a fixed-format header followed by a variable-format options area. All values in the message header and options are encoded in network byte order.

### Message Types
| Code | Name | Description | RFC |
| :--- | :--- | :--- | :--- |
| 1 | SOLICIT | Client initiates to locate available servers. | RFC 8415 |
| 2 | ADVERTISE | Server indicates availability in response to Solicit. | RFC 8415 |
| 3 | REQUEST | Client requests parameters/addresses from a specific server. | RFC 8415 |
| 4 | CONFIRM | Client verifies if assigned addresses remain valid for the link. | RFC 8415 |
| 5 | RENEW | Client requests lease extension from the original server. | RFC 8415 |
| 6 | REBIND | Client requests lease extension from any available server after a RENEW fails. | RFC 8415 |
| 7 | REPLY | Server provides leases, parameters, or acknowledgments in response to client messages. | RFC 8415 |
| 8 | RELEASE | Client notifies the issuing server that it is relinquishing one or more leases. | RFC 8415 |
| 9 | DECLINE | Client notifies server that assigned addresses are already in use on the link. | RFC 8415 |
| 10 | RECONFIGURE | Server prompts client to initiate a transaction to receive updated parameters. | RFC 8415 |
| 11 | INFORMATION-REQUEST | Client requests configuration parameters without lease assignments. | RFC 8415 |
| 12 | RELAY-FORW | Relay agent forwards a message to a server. Original message is encapsulated. | RFC 8415 |
| 13 | RELAY-REPL | Server sends response to a relay agent; response for client is encapsulated. | RFC 8415 |
| 14 | LEASEQUERY | Requestor queries a server to retrieve information regarding a client's lease state. | RFC 5007 |
| 15 | LEASEQUERY-REPLY | Server responds to a LEASEQUERY with requested client lease data or status. | RFC 5007 |
| 16 | LEASEQUERY-DONE | Signals the termination of a leasequery response stream. | RFC 5460 |
| 17 | LEASEQUERY-DATA | Transmits data for a single client's leases or Prefix Delegation (PD) bindings. | RFC 5460 |
| 18 | RECONFIGURE-REQUEST | | RFC 6977 |
| 19 | RECONFIGURE-REPLY | | RFC 6977 |
| 20 | DHCPV4-QUERY | Client sends this to a DHCP 4o6 server. Contains DHCPv4 message in an option. | RFC 7341 |
| 21 | DHCPV4-RESPONSE | Server responds to client. Carries resulting DHCPv4 message inside an option. | RFC 7341 |
| 22 | ACTIVELEASEQUERY | Provide real-time updates on DHCPv6 binding activity. | RFC 7653 |
| 23 | STARTTLS | Initiates the establishment of a Transport Layer Security (TLS) connection. | RFC 7653 |

### Option Codes
| Option-Code | Name | Description | RFC |
| :--- | :--- | :--- | :--- |
| 1 | OPTION_CLIENTID | Carries a DUID to uniquely identify the client. | RFC 8415 |
| 2 | OPTION_SERVERID | Carries a DUID to uniquely identify the server. | RFC 8415 |
| 3 | OPTION_IA_NA | Container for non-temporary address assignments and associated parameters. | RFC 8415 |
| 4 | OPTION_IA_TA | Container for temporary address assignments. | RFC 8415 |
| 5 | OPTION_IAADDR | Specifies an IPv6 address associated with an IA_NA or IA_TA. | RFC 8415 |
| 6 | OPTION_ORO | Identifies a list of requested options by their option codes. | RFC 8415 |
| 7 | OPTION_PREFERENCE | Provides a preference value to influence the client's server selection. | RFC 8415 |
| 8 | OPTION_ELAPSED_TIME | Reports duration of current DHCP transaction in hundredths of a second. | RFC 8415 |
| 9 | OPTION_RELAY_MSG | Encapsulates a DHCPv6 message for transmission between relay agents and servers. | RFC 8415 |
| 11 | OPTION_AUTH | Carries authentication information to verify the identity of the sender. | RFC 8415 |
| 12 | OPTION_UNICAST | Indicates a server unicast address the client may use for direct contact. | RFC 8415 |
| 13 | OPTION_STATUS_CODE | Communicates success or failure status and associated error messages. | RFC 8415 |
| 14 | OPTION_RAPID_COMMIT | Signals the use of a two-message exchange for address assignment. | RFC 8415 |
| 15 | OPTION_USER_CLASS | Identifies the type or category of user or application on the client. | RFC 8415 |
| 16 | OPTION_VENDOR_CLASS | Identifies the vendor and hardware/software configuration of the client. | RFC 8415 |
| 17 | OPTION_VENDOR_OPTS | Carries vendor-specific information and parameters. | RFC 8415 |
| 18 | OPTION_INTERFACE_ID | Identifies the specific interface on which a relay agent received a message. | RFC 8415 |
| 19 | OPTION_RECONF_MSG | Specifies the message type a client must use when responding to reconfiguration. | RFC 8415 |
| 20 | OPTION_RECONF_ACCEPT | Signals that the client supports and will accept Reconfigure messages. | RFC 8415 |
| 25 | OPTION_IA_PD | Container for prefix delegation identity associations and parameters. | RFC 8415 |
| 26 | OPTION_IAPREFIX | Specifies an IPv6 prefix associated with an IA_PD. | RFC 8415 |
| 32 | OPTION_INFORMATION_REFRESH_TIME | Defines the interval at which a client should refresh its configuration information. | RFC 8415 |
| 82 | OPTION_SOL_MAX_RT | Defines the maximum retransmission timeout for Solicit messages. | RFC 8415 |
| 83 | OPTION_INF_MAX_RT | Defines the maximum retransmission timeout for Information-request messages. | RFC 8415 |

### Option Request Option (ORO)
The Option Request Option (ORO), identified by OPTION_ORO (6), is the mechanism used by a DHCPv6 client to inform the server which configuration parameters it is interested in receiving. Rather than the server blindly pushing all available data, the client provides a list of option codes within the ORO to tailor the response to its specific needs.

*   **Client Responsibility**: The client MUST include an ORO in messages like Solicit, Request, Renew, and Rebind if it requires specific information (such as DNS recursive name servers or domain search lists).
*   **Server Responsibility**: The server uses the ORO as a guide. It should include the requested options in its response, provided those options are configured and appropriate for the client's link.

#### Common DHCPv6 Option Request Codes
| Code | Name | Function |
| :--- | :--- | :--- |
| 23 | DNS_SERVERS | Requests a list of IPv6 addresses for recursive DNS servers. |
| 24 | DOMAIN_LIST | Requests the domain search list for suffix completion. |
| 31 | SNTP_SERVERS | Requests a list of Simple Network Time Protocol (SNTP) servers. |
| 32 | INF_REFRESH_TIME | Requests the interval for when to refresh stateless information. |
| 56 | NTP_SERVER | Requests Network Time Protocol (NTP) server information (RFC 5908). |
| 59 | BOOTFILE_URL | Used in PXE booting to request the location of a boot image. |

## IETF Standards
*   **RFC 8415**: "Dynamic Host Configuration Protocol for IPv6 (DHCPv6)" - Obsoletes RFC 3315, RFC 3633, RFC 3736, RFC 4242, RFC 7083, RFC 7283, RFC 7550.
*   **RFC 3319**: "Dynamic Host Configuration Protocol (DHCPv6) Options for Session Initiation Protocol (SIP) Servers"
*   **RFC 3646**: "DNS Configuration options for Dynamic Host Configuration Protocol for IPv6 (DHCPv6)"
*   **RFC 4704**: "The Dynamic Host Configuration Protocol for IPv6 (DHCPv6) Client Fully Qualified Domain Name (FQDN) Option"
*   **RFC 5007**: "DHCPv6 Leasequery" support
*   **RFC 6221**: "Lightweight DHCPv6 Relay Agent" (LDRA) - Updates RFC 3315, Errata
*   **RFC 6355**: "Definition of the UUID-Based DHCPv6 Unique Identifier (DUID-UUID)"
*   **RFC 6939**: "Client Link-Layer Address Option in DHCPv6"
*   **RFC 7653**: "DHCPv6 Active Leasequery"
