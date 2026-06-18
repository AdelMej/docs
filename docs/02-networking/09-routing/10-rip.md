# Routing Information Protocol (RIP)

The Routing Information Protocol (RIP) is one of the oldest distance-vector routing protocols, employing the hop count as its primary routing metric. To prevent routing loops, RIP implements a limit on the number of hops allowed in a path from source to destination. The maximum number of hops permitted for RIP is 15; any network beyond this limit is considered unreachable. Additionally, RIP utilizes split horizon, route poisoning, and holddown mechanisms to ensure that incorrect routing information is not propagated throughout the network.

While RIPv1 routers broadcast routing table updates every 30 seconds, this can lead to massive traffic bursts in large networks. Consequently, in most modern networking environments, RIP is not the preferred choice due to poor scalability and convergence times compared to protocols like EIGRP, OSPF, or IS-IS. However, it remains popular for simple setups because it requires no complex parameters for configuration. RIP uses the User Datagram Protocol (UDP) as its transport protocol on reserved port 520.

## Development of Distance-Vector Routing

Distance-vector routing protocols originated in the 1960s based on the Bellman–Ford and Ford–Fulkerson algorithms. The predecessor to RIP was the Gateway Information Protocol (GWINFO), developed by Xerox in the mid-1970s. This evolved into the XNS Routing Information Protocol, which served as the foundation for early protocols like Novell's IPX RIP and AppleTalk's Routing Table Maintenance Protocol (RTMP). The 4.2BSD release of the Berkeley Software Distribution implemented RIP in its routed daemon, which became the basis for subsequent UNIX versions. RIP was eventually standardized as RIPv1 in 1988.

## The RIP Hop Count

The routing metric in RIP counts how many routers must be passed to reach a destination IP network.
*   **0 Hops:** Indicates the network is directly connected to the router.
*   **16 Hops:** Denotes a network that is unreachable according to the RIP hop limit.

## Versions of RIP

There are three standardized versions of the protocol:

### RIP Version 1
Published in 1988, RIPv1 routers broadcast request messages every 30 seconds. Neighboring routers respond with segments containing their routing tables. The requesting router then updates its own table with reachable networks and the lowest hop counts. To avoid synchronizing updates across a LAN, RIPv1 adds a small random time variable to the update interval.

RIPv1 is primarily limited because it uses classful routing, meaning it does not carry subnet information and cannot support Variable Length Subnet Masks (VLSM). It also lacks support for router authentication, making it vulnerable to various attacks. A "silent mode" can be configured so that a router processes updates from neighbors without broadcasting its own table into the network.

### RIP Version 2
Developed in 1993 and declared an Internet Standard in 1998, RIPv2 was designed to address the deficiencies of the original specification. It supports Classless Inter-Domain Routing (CIDR) by carrying subnet information. To maintain backward compatibility, it retains the 15-hop limit.

Key improvements in RIPv2 include:
*   **Multicast:** Updates are sent to all adjacent routers at 224.0.0.9 instead of broadcasting.
*   **Authentication:** MD5 authentication was introduced in 1997.
*   **Route Tags:** Allows for distinguishing between routes learned via RIP and those learned from other protocols.

### RIPng (Next Generation)
RIPng is an extension of RIPv2 designed to support IPv6 networking. The primary differences include:
*   Support for IPv6 networking.
*   Lack of built-in authentication (relying on IPsec instead).
*   Specific encoding requirements for the next hop in route entries.
*   Usage of UDP port 521 and the multicast group ff02::9.

## RIP Messages Between Routers

RIP messages are encapsulated in UDP datagrams on port 520. There are two primary message types:
1.  **Request Message:** Asks a neighboring router to send its routing table.
2.  **Response Message:** Carries the actual routing table of a router.

## Timers

RIP uses several timers to manage the stability of the routing table:
*   **Update Timer:** Controls the interval between gratuitous Response Messages (default is 30 seconds).
*   **Invalid Timer:** Specifies how long an entry can remain in the table without an update before it is marked unreachable. The default is 180 seconds, at which point the hop count is set to 16.
*   **Flush Timer:** Controls the time between a route being marked unreachable and its final removal from the table (default is 240 seconds).
*   **Holddown Timer:** A Cisco-specific implementation that prevents updates on a route entry when the hop count increases, allowing the route to stabilize (default is 180 seconds).

## Limitations

*   The hop count cannot exceed 15.
*   RIP version 1 does not support Variable Length Subnet Masks (VLSM).
*   The protocol suffers from slow convergence and "count to infinity" problems.

## Implementations

Several systems implement RIP, including:
*   **Cisco:** IOS and NX-OS (Note: NX-OS supports RIPv2 only).
*   **Juniper:** Junos software.
*   **Microsoft:** Routing and Remote Access feature in Windows Server.
*   **Open Source:** Quagga, BIRD, and Zeroshell.
*   **Operating Systems:** FreeBSD, NetBSD, and OpenBSD (ripd).
*   **Hardware:** Netgear routers commonly offer RIP_2M (multicast) and RIP_2B (broadcast) implementations.

## Similar Protocols

Cisco's proprietary Interior Gateway Routing Protocol (IGRP) was a more capable distance-vector protocol that was eventually replaced by the Enhanced Interior Gateway Routing Protocol (EIGRP). While EIGRP is a new design, it retains the same composite routing metric as IGRP (bandwidth, delay, reliability, load, and MTU).
