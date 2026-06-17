# Border Gateway Protocol

Border Gateway Protocol (BGP) is a standardized exterior gateway protocol designed to exchange routing and reachability information among autonomous systems (AS) on the Internet. BGP is classified as a path-vector routing protocol, and it makes routing decisions based on paths, network policies, or rule-sets configured by a network administrator.

BGP used for routing within an autonomous system is called Interior Border Gateway Protocol (iBGP). In contrast, the Internet application of the protocol is called Exterior Border Gateway Protocol (EBGP).

## History

In January 1989, at the 12th IETF meeting in Austin, Texas, Yakov Rekhter, Len Bosack, and Kirk Lougheed designed what ultimately became the Border Gateway Protocol. The initial design was recorded on two napkins, hence often referenced as the “Two-Napkin Protocol.” The design was expanded to three hand-written sheets of paper from which the first interoperable BGP implementation was quickly developed.

In 1989, RFC 1105 was published, and the BGP protocol has been in use on the Internet since 1994. The protocol definition changed in 1990 (RFC 1136). In October 1991, BGP version 3 was defined in RFC 1267. In 1994, the current version (BGP4) was published as RFC 1654, and its definition was replaced in March 1995 by RFC 1771. In January 2006, RFC 4271 was published, which is currently the latest definition of BGP4.

RFC 4271 corrected errors, clarified ambiguities, and updated the specification with common industry practices. The major enhancement of BGP4 was the support for Classless Inter-Domain Routing (CIDR) and the use of route aggregation to decrease the size of routing tables. Since 1998, routing information about a wide range of "address families" (IPv4, IPv6, IPX, etc.) can be carried via Multiprotocol Extensions.

## Operation

BGP neighbors, called peers, are established by manual configuration among routers to create a TCP session on port 179. A BGP speaker sends 19-byte keep-alive messages every 30 seconds (protocol default) to maintain the connection. Among routing protocols, BGP is unique in using TCP as its transport protocol.

### iBGP vs eBGP

* **Internal BGP (iBGP):** Runs between two peers in the same autonomous system.
* **External BGP (eBGP):** Runs between different autonomous systems.

The main difference is how routes are propagated by default:

* New routes learned from an eBGP peer are re-advertised to all iBGP and eBGP peers.
* New routes learned from an iBGP peer are re-advertised to all eBGP peers only.

These rules effectively require that all iBGP peers inside an AS are interconnected in a full mesh. Route propagation can be controlled in detail via the route-maps mechanism, which consists of a set of rules to drop or modify routes based on specific criteria.

## Extensions Negotiation

During the peering handshake, BGP speakers negotiate optional capabilities, including multiprotocol extensions and various recovery modes. If multiprotocol extensions are negotiated, the BGP speaker can prefix the Network Layer Reachability Information (NLRI) with an address family prefix (e.g., IPv4, IPv6, VPNs, or multicast).

BGP uses a simple finite-state machine (FSM) consisting of six states:

1. **Idle:** Initializes resources, refuses inbound connections, and initiates TCP connection.
2. **Connect:** Waits for the TCP connection to complete.
3. **Active:** Resets the ConnectRetry timer and attempts to restart the TCP session.
4. **OpenSent:** Sends an Open message and waits for a response.
5. **OpenConfirm:** Receives a Keepalive message and verifies timers.
6. **Established:** Peers can send and receive Update, Keepalive, and Notification messages.

## Router Connectivity and Learning Routes

In the simplest arrangement, all routers within a single AS participating in BGP must be configured in a full mesh. To alleviate scaling problems, BGP implements two options:

* **Route Reflectors (RRs):** A central router acts as an RR server, allowing other routers (clients) to peer with it instead of every other router.
* **Confederations:** A large AS is configured to encompass smaller, more manageable internal ASs. To the outside world, the confederation appears as a single AS.

BGP maintains several routing information bases:

* **RIB:** The router's main routing information base table.
* **Loc-RIB:** BGP’s own master routing table separate from the main table.
* **Adj-RIB-In:** Incoming NLRI received from each neighbor.
* **Adj-RIB-Out:** Outgoing NLRI sent to each neighbor.

## Route Selection Process

BGP follows a specific order of tiebreakers to determine the best path for NLRI to enter the Loc-RIB:

1. **Local Weight:** Highest (Cisco-specific).
2. **Local Preference:** Higher.
3. **Accumulated Interior Gateway Protocol (AIGP):** Lowest.
4. **Autonomous System (AS) Jumps:** Lowest (shortest AS_PATH).
5. **Origin Type:** Lowest (e.g., IGP preferred over EGP).
6. **Multi-Exit Discriminator (MED):** Lowest.
7. **eBGP over iBGP paths:** Preferred.
8. **IGP metric to BGP next hop:** Lowest.
9. **Path received first:** Oldest.
10. **Neighbor Router ID:** Lowest.
11. **Cluster list length:** Lowest.
12. **Neighbor IP address:** Lowest.

## Communities

BGP communities are attribute tags applied to prefixes to achieve common goals, such as geographic restrictions or peer type limitations.

* **Well-known communities** include `GRACEFUL_SHUTDOWN`, `NO_EXPORT`, `NO_ADVERTISE`, and `BLACKHOLE`.
* **Extended Communities** (added in 2006) extend the range of attributes and provide structuring by means of a type field. They are used for things like Route Target Communities and QoS signaling.
* **Large Communities** were defined to accommodate 32-bit AS numbers, divided into three fields (AS:function:parameter).

## Multi-Exit Discriminators (MED)

MEDs were originally intended to show a neighbor AS the advertising AS's preference for which of several links are preferred for inbound traffic. Some routers use metrics from other protocols (like OSPF) to set the MED value.

## Packet Format

All BGP messages share a common header containing:

* **Marker:** 128 bits (all ones).
* **Length:** 16 bits (total length of the message).
* **Type:** 8 bits (Open, Update, Notification, KeepAlive, Route-Refresh).

Specific packet types include:

* **Open Packet:** Initiates a session, containing Version, My AS, Hold Time, BGP Identifier, and optional parameters.
* **Update Packet:** Sends changes, including withdrawn routes, path attributes, and NLRI.
* **Notification:** Sent if there is an error in the OPEN or UPDATE message.
* **KeepAlive:** Sent periodically to verify the peer is still alive.
* **Route-Refresh:** Allows soft updating of Adj-RIB-In without resetting the connection.

## Stability and Routing Table Growth

### Route Flapping

Route flapping occurs when a router cycles rapidly between up and down states. **Route flap damping** is used to mitigate this by exponentially decaying the reachability of a flapping prefix for a set period.

### Routing Table Growth

The growth of the Internet routing table is a major challenge. To prevent breakdown, ISPs use **CIDR** and **route aggregation**.

* **512k Day:** In 2014, some older routers hit a 512,000 prefix limit, causing outages for services like eBay and Microsoft Azure.
* **AS Number Depletion:** BGP was extended from 16-bit to 32-bit AS numbers in 2007 to accommodate the growth of the Internet.

## Security and Extensions

### Security

BGP accepts advertised routes by default, making it vulnerable to **BGP hijacking**. Correcting this via cryptographic keys is a significant technical and economic challenge.

### Extensions

* **Multiprotocol BGP (MBGP):** Allows different address families (IPv4, IPv6) to be distributed in parallel.
* **Multipath Routing:** Allows the advertisement of multiple paths for the same destination (RFC 7911).

## Uses and Implementations

BGP is standard for Internet routing and is required by most ISPs. It is also used internally by large private networks and for multihoming to improve redundancy.

**Notable open-source implementations include:**

* BIRD
* FRRouting (fork of Quagga)
* Quagga
* GNU Zebra
* OpenBGPD
* XORP
