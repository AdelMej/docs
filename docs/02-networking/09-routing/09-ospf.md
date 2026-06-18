# Open Shortest Path First (OSPF)

Open Shortest Path First (OSPF) is a routing protocol for Internet Protocol (IP) networks. It uses a link state routing (LSR) algorithm and falls into the group of interior gateway protocols (IGPs), operating within a single autonomous system (AS).

OSPF gathers link state information from available routers and constructs a topology map of the network. The topology is presented as a routing table to the internet layer for routing packets by their destination IP address. OSPF supports Internet Protocol version 4 (IPv4) and Internet Protocol version 6 (IPv6) networks and is widely used in large enterprise networks. IS-IS, another LSR-based protocol, is more common in large service provider networks.

Originally designed in the 1980s, OSPF version 2 is defined in RFC 2328 (1998). The updates for IPv6 are specified as OSPF version 3 in RFC 5340 (2008). OSPF supports the Classless Inter-Domain Routing (CIDR) addressing model.

## Concepts

OSPF is an interior gateway protocol (IGP) for routing Internet Protocol (IP) packets within a single routing domain, such as an autonomous system. It gathers link state information from available routers and constructs a topology map of the network. The topology is presented as a routing table to the internet layer, which routes packets based solely on their destination IP address.

OSPF detects changes in the topology, such as link failures, and converges on a new loop-free routing structure within seconds. It computes the shortest-path tree for each route using a method based on Dijkstra's algorithm. The OSPF routing policies for constructing a route table are governed by link metrics associated with each routing interface. Cost factors may be the distance of a router (round-trip time), data throughput of a link, or link availability and reliability, expressed as simple unitless numbers. This provides a dynamic process of traffic load balancing between routes of equal cost.

OSPF divides the network into routing areas to simplify administration and optimize traffic and resource utilization. Areas are identified by 32-bit numbers, expressed either simply in decimal or often in the same octet-based dot-decimal notation used for IPv4 addresses. By convention, area 0 (zero), or 0.0.0.0, represents the core or backbone area of an OSPF network. While the identifications of other areas may be chosen at will, administrators often select the IP address of a main router in an area as the area identifier. Each additional area must have a connection to the OSPF backbone area. Such connections are maintained by an interconnecting router, known as an area border router (ABR). An ABR maintains separate link-state databases for each area it serves and maintains summarized routes for all areas in the network.

OSPF runs over IPv4 and IPv6, but does not use a transport protocol such as UDP or TCP. It encapsulates its data directly in IP packets with protocol number 89. This is in contrast to other routing protocols, such as the Routing Information Protocol (RIP) and the Border Gateway Protocol (BGP). OSPF implements its own transport error detection and correction functions. OSPF also uses multicast addressing for distributing route information within a broadcast domain. It reserves the multicast addresses 224.0.0.5 (IPv4) and ff02::5 (IPv6) for all SPF/link state routers (AllSPFRouters) and 224.0.0.6 (IPv4) and ff02::6 (IPv6) for all Designated Routers (AllDRouters). For non-broadcast networks, special provisions for configuration facilitate neighbor discovery. OSPF multicast IP packets never traverse IP routers, they never travel more than one hop. The protocol may therefore be considered a link-layer protocol, but is often also attributed to the application layer in the TCP/IP model. It has a virtual link feature that can be used to create an adjacency tunnel across multiple hops. OSPF over IPv4 can operate securely between routers, optionally using a variety of authentication methods to allow only trusted routers to participate in routing. OSPFv3 (IPv6) relies on standard IPv6 protocol security (IPsec), and has no internal authentication methods.

For routing IP multicast traffic, OSPF supports the Multicast Open Shortest Path First (MOSPF) protocol. Cisco does not include MOSPF in their OSPF implementations. Protocol Independent Multicast (PIM) in conjunction with OSPF or other IGPs, is widely deployed.

OSPF version 3 introduces modifications to the IPv4 implementation of the protocol. Except for virtual links, all neighbor exchanges use IPv6 link-local addressing exclusively. The IPv6 protocol runs per link, rather than based on the subnet. All IP prefix information has been removed from the link-state advertisements and from the hello discovery packet, making OSPFv3 essentially protocol-independent. Despite the expanded IP addressing to 128 bits in IPv6, area and router Identifications are still based on 32-bit numbers.

## Router Relationships

OSPF supports complex networks with multiple routers, including backup routers, to balance traffic load on multiple links to other subnets. Neighboring routers in the same broadcast domain or at each end of a point-to-point link communicate with each other via the OSPF protocol. Routers form adjacencies when they have detected each other. This detection is initiated when a router identifies itself in a hello protocol packet. Upon acknowledgment, this establishes a two-way state and the most basic relationship. The routers in an Ethernet or Frame Relay network select a designated router (DR) and a backup designated router (BDR), which act as a hub to reduce traffic between routers. OSPF uses both unicast and multicast transmission modes to send "hello" packets and link-state updates.

As a link-state routing protocol, OSPF establishes and maintains neighbor relationships for exchanging routing updates with other routers. The neighbor relationship table is called an adjacency database. Two OSPF routers are neighbors if they are members of the same subnet and share the same area ID, subnet mask, timers and authentication. In essence, OSPF neighborship is a relationship between two routers that allows them to see and understand each other but nothing more. OSPF neighbors do not exchange any routing information – the only packets they exchange are hello packets. OSPF adjacencies are formed between selected neighbors and allow them to exchange routing information. Two routers must first be neighbors, and only then can they become adjacent. Two routers become adjacent if at least one of them is designated router or backup designated router (on multiaccess-type networks), or they are interconnected by a point-to-point or point-to-multipoint network type. For forming a neighbor relationship between the interfaces, the relationship must be in the same OSPF area. While an interface may be configured to belong to multiple areas, this is generally not practiced. When configured in a second area, an interface must be configured as a secondary interface.

### Operation Modes

The OSPF can have different operation modes on the following setups on an interface or network:

* **Point-to-point:** Each router advertises itself by periodically multicasting hello packets. No designated router is elected. The interface can be IP unnumbered (without a unique IP address assigned to it).
* **Broadcast (default):** Each router advertises itself by periodically multicasting hello packets.
* **Non-broadcast multi-access:** With the use of designated routers. May need static configuration. Packets are sent as unicast.
* **Point-to-multipoint:** Where OSPF treats neighbours as a collection of point-to-point links. No designated router is elected. Separate hello packets are sent to each neighbor.
* **Point to Multipoint Non Broadcast (P2MP-NB):** No designated router is elected. Separate hello packets are sent to each neighbor. Packets are sent as unicast.
* **Passive:** Only advertised to other neighbours. No adjacency is advertised on network.

### Indirect Connections

* **Virtual links:** The packets are sent as unicast. Can only be configured on a non-backbone area (but not stub-area). Endpoints need to be ABR, the virtual links behave as unnumbered point-to-point connections. The cost of an intra-area path between the two routers is added to the link.
* **Virtual link over tunneling (like GRE and WireGuard):** Since OSPF does not support virtual links for areas other than the backbone, a workaround is the use of tunneling. If the same IP or router ID is used, the link creates two equal-cost routes to the destination.
* **Sham link:** An intra-area link that connects two sites via the MPLS VPN backbone that is preferred to an internal intra-area "OSPF backdoor link" between the same two sites. A sham link is only needed if the MPLS VPN backbone is preferred over the OSPF backdoor link.

### Adjacency State Machine

Each OSPF router within a network communicates with other neighboring routers on each connecting interface to establish the states of all adjacencies. Every such communication sequence is a separate conversation identified by the pair of router IDs of the communicating neighbors. RFC 2328 specifies the protocol for initiating these conversations (Hello Protocol) and for establishing full adjacencies (database description packets, link-state request packets). During its course, each router conversation transitions through a maximum of eight conditions defined by a state machine:

**Neighbor State Changes (Hello Protocol)**

* **Down:** The state down represents the initial state of a conversation when no information has been exchanged and retained between routers with the Hello Protocol.
* **Attempt:** The attempt state is similar to the down state, except that a router is in the process of efforts to establish a conversation with another router, but is only used on non-broadcast multiple-access networks (NBMAs).
* **Init:** The init state indicates that a hello packet has been received from a neighbor, but the router has not established a two-way conversation.
* **Two-way:** The two-way state indicates the establishment of a bidirectional conversation between two routers. This state immediately precedes the establishment of adjacency. This is the lowest state of a router that may be considered as a DR.

**Database Exchange**

* **Exchange start (exstart):** The exstart state is the first step of adjacency of two routers.
* **Exchange:** In the exchange state, a router is sending its link-state database information to the adjacent neighbor. At this state, a router can exchange all OSPF routing protocol packets.
* **Loading:** In the loading state, a router requests the most recent link-state advertisements (LSAs) from its neighbor discovered in the previous state.
* **Full:** The full state concludes the conversation when the routers are fully adjacent, and the state appears in all router- and network-LSAs. The link-state databases of the neighbors are fully synchronized.

## Broadcast Networks

In broadcast multiple-access networks, neighbor adjacency is formed dynamically using multicast hello packets to 224.0.0.5.

## Passive Network

A network where OSPF advertises the network, but OSPF will not start neighbor adjacency.

## Non-broadcast Networks

In a non-broadcast multiple-access (NBMA) network, a neighbor adjacency is formed by sending unicast packets to another router. A non-broadcast network can have more than two routers, but broadcast is not supported. Examples of non-broadcast networks include:

* X.25 public data network
* WireGuard
* Serial interface

Requires all routers to be able to communicate directly, on the same network. A Designated Router is elected for the network. LSA is generated for the network.

## OSPF Areas

A network is divided into OSPF areas that are logical groupings of contiguous hosts and networks. An area includes its connecting router, having an interface for each connected network link. Each router maintains a separate link-state database for the area whose information may be summarized towards the rest of the network by the connecting router. Thus, the topology of an area is unknown outside the area. This reduces the routing traffic between parts of an autonomous system.

Though OSPF can handle thousands of routers, there is a concern reaching the capacity of the forwarding information base (FIB) table when the network contains lots of routes and lower-end devices. Modern low-end routers have a full gigabyte of RAM, which allows them to handle many routers in an area 0. Many resources refer to OSPF guides from over 20 years ago, where it was impressive to have 64 MB of RAM.

Areas are uniquely identified with 32-bit numbers. The area identifiers are commonly written in the dot-decimal notation, familiar from IPv4 addressing. However, they are not IP addresses and may duplicate, without conflict, any IPv4 address. The area identifiers for IPv6 implementations (OSPFv3) also use 32-bit identifiers written in the same notation. When dotted formatting is omitted, most implementations expand area 1 to the area identifier 0.0.0.1, but some have been known to expand it as 1.0.0.0.

Several vendors (Cisco, Allied Telesis, Juniper, Alcatel-Lucent, Huawei, Quagga) implement totally stubby and NSSA totally stubby area for stub and not-so-stubby areas. Although not covered by RFC standards, they are considered by many to be standard features in OSPF implementations.

OSPF defines several area types:

* Backbone
* Non-backbone/regular
* Stub
* Totally stubby
* Not-so-stubby
* Totally not-so-stubby
* Transit

### Backbone Area

The backbone area (also known as area 0 or area 0.0.0.0) forms the core of an OSPF network. All other areas are connected to it, either directly or through other routers. OSPF requires this to prevent routing loops. Inter-area routing happens via routers connected to the backbone area and to their own associated areas. It is the logical and physical structure for the 'OSPF domain' and is attached to all nonzero areas in the OSPF domain. In OSPF the term autonomous system boundary router (ASBR) is historic, in the sense that many OSPF domains can coexist in the same Internet-visible autonomous system, RFC 1996.

All OSPF areas must connect to the backbone area. This connection, however, can be through a virtual link. For example, assume area 0.0.0.1 has a physical connection to area 0.0.0.0. Further assume that area 0.0.0.2 has no direct connection to the backbone, but this area does have a connection to area 0.0.0.1. Area 0.0.0.2 can use a virtual link through the transit area 0.0.0.1 to reach the backbone. To be a transit area, an area has to have the transit attribute, so it cannot be stubby in any way.

### Regular Area

A regular area is just a non-backbone (nonzero) area without a specific feature, generating and receiving summary and external LSAs. The backbone area is a special type of such area.

### Stub Area

A stub area is an area that does not receive route advertisements external to the AS, and routing from within the area is based entirely on a default route. An ABR deletes type 4 and 5 LSAs from internal routers, sends them a default route of 0.0.0.0, and turns itself into a default gateway. This reduces LSDB and routing table size for internal routers.

Modifications to the basic concept of stub area have been implemented by systems vendors, such as the totally stubby area (TSA) and the not-so-stubby area (NSSA), both extensions in Cisco Systems routing equipment.

### Totally Stubby Area

A totally stubby area is similar to a stub area. However, this area does not allow summary routes in addition to not having external routes; that is, inter-area (IA) routes are not summarized into totally stubby areas. The only way for traffic to get routed outside the area is a default route, which is the only Type-3 LSA advertised into the area. When there is only one route out of the area, fewer routing decisions have to be made by the route processor, which lowers system resource utilization.

Occasionally, it is said that a TSA can have only one ABR.

### Not-so-Stubby Area (NSSA)

A not-so-stubby area (NSSA) is a type of stub area that can import autonomous system external routes and send them to other areas, but still cannot receive AS-external routes from other areas.

NSSA is an extension of the stub area feature that allows the injection of external routes in a limited fashion into the stub area. A case study simulates an NSSA getting around the stub-area problem of not being able to import external addresses. It visualizes the following activities: the ASBR imports external addresses with a type 7 LSA, the ABR converts a type 7 LSA to type 5 and floods it to other areas, the ABR acts as an ASBR for other areas.

The ASBRs do not take type 5 LSAs and then convert to type 7 LSAs for the area.

### Totally Not-so-Stubby Area

An addition to the standard functionality of an NSSA, the totally stubby NSSA is an NSSA that takes on the attributes of a TSA, meaning that type 3 and 4 summary routes are not flooded into this type of area. It is also possible to declare an area both totally stubby and not-so-stubby, which means that the area will receive only the default route from area 0.0.0.0, but can also contain an autonomous system boundary router (ASBR) that accepts external routing information and injects it into the local area, and then into area 0.0.0.0.

Redistribution into an NSSA area creates a special type of LSA known as type 7, which can exist only in an NSSA area. An NSSA ASBR generates this LSA, and an NSSA ABR router translates it into a type 5 LSA, which gets propagated into the OSPF domain.

A newly acquired subsidiary is one example of where it might be suitable for an area to be simultaneously not-so-stubby and totally stubby if the practical place to put an ASBR is on the edge of a totally stubby area. In such a case, the ASBR does send externals into the totally stubby area, and they are available to OSPF speakers within that area. In Cisco's implementation, the external routes can be summarized before injecting them into the totally stubby area. In general, the ASBR should not advertise default into the TSA-NSSA, although this can work with extremely careful design and operation, for the limited special cases in which such an advertisement makes sense.

By declaring the totally stubby area as NSSA, no external routes from the backbone, except the default route, enter the area being discussed. The externals do reach area 0.0.0.0 via the TSA-NSSA, but no routes other than the default route enter the TSA-NSSA. Routers in the TSA-NSSA send all traffic to the ABR, except to routes advertised by the ASBR.

## Router Types

OSPF defines the following overlapping categories of routers:

* **Internal router (IR):** An internal router has all its interfaces belonging to the same area.
* **Area border router (ABR):** An area border router is a router that connects one or more areas to the main backbone network. It is considered a member of all areas it is connected to. An ABR keeps multiple instances of the link-state database in memory, one for each area to which that router is connected.
* **Backbone router (BR):** A backbone router has an interface to the backbone area. Backbone routers may also be area border routers, but do not have to be.
* **Autonomous system boundary router (ASBR):** An autonomous system boundary router is a router that is connected by using more than one routing protocol and that exchanges routing information with routers autonomous systems. ASBRs typically also run an exterior routing protocol (e.g., BGP), or use static routes, or both. An ASBR is used to distribute routes received from other, external ASs throughout its own autonomous system. An ASBR creates External LSAs for external addresses and floods them to all areas via ABR. Routers in other areas use ABRs as next hops to access external addresses. Then ABRs forward packets to the ASBR that announces the external addresses.

The router type is an attribute of an OSPF process. A given physical router may have one or more OSPF processes. For example, a router that is connected to more than one area, and which receives routes from a BGP process connected to another AS, is both an area border router and an autonomous system boundary router.

Each router has an identifier, customarily written in the dotted-decimal format (e.g., 1.2.3.4) of an IP address. This identifier must be established in every OSPF instance. If not explicitly configured, the highest logical IP address will be duplicated as the router identifier. However, since the router identifier is not an IP address, it does not have to be a part of any routable subnet in the network, and often isn't to avoid confusion.

## Non-point-to-point Network

On networks (same subnet) with networks type of: Broadcast, Non-Broadcast Multi-Access (NBMA), Point-to-multipoint, or Point to Multipoint Non Broadcast (P2MP-NB).

A system of designated router (DR) and backup designated router (BDR), is used to reduce network traffic by providing a source for routing updates. This is done using multicast addresses:

* **224.0.0.5:** All routers in the topology will listen on that multicast address.
* **224.0.0.6:** DR and BDR will listen on that multicast address.

The DR and BDR maintains a complete topology table of the network and sends the updates to the other routers via multicast. All routers in a multi-access network segment will form a leader/follower relationship with the DR and BDR. They will form adjacencies with the DR and BDR only. Every time a router sends an update, it sends it to the DR and BDR on the multicast address 224.0.0.6. The DR will then send the update out to all other routers in the area, to the multicast address 224.0.0.5. This way all the routers do not have to constantly update each other, and can rather get all their updates from a single source. The use of multicasting further reduces the network load. DRs and BDRs are always setup/elected on OSPF broadcast networks. DRs can also be elected on NBMA (Non-Broadcast Multi-Access) networks such as Frame Relay or ATM. DRs or BDRs are not elected on point-to-point links (such as a point-to-point WAN connection) because the two routers on either side of the link must become fully adjacent and the bandwidth between them cannot be further optimized.

### Designated Router

A designated router (DR) is the router interface elected among all routers on a particular multiaccess network segment, generally assumed to be broadcast multiaccess. Special techniques, often vendor-dependent, may be needed to support the DR function on non-broadcast multiaccess (NBMA) media. It is usually wise to configure the individual virtual circuits of an NBMA subnet as individual point-to-point lines; the techniques used are implementation-dependent.

### Backup Designated Router

A backup designated router (BDR) is a router that becomes the designated router if the current designated router has a problem or fails. The BDR is the OSPF router with the second-highest priority at the time of the last election.

A given router can have some interfaces that are designated (DR) and others that are backup designated (BDR), and others that are non-designated. If no router is a DR or a BDR on a given subnet, the BDR is first elected, and then a second election is held for the DR.

### DR Other

A router that has not been selected to be designated router (DR) or backup designated router (BDR). The router forms an adjacency to both the designated router (DR) and the backup designated router (BDR). For other non (B)DR, the adjacency stops at 2-ways State.

### Designated Router Election

The DR is elected based on the following default criteria:

1. If the priority setting on an OSPF router is set to 0, that means it can NEVER become a DR or BDR.
2. If no DR exists on the network, routes will wait until Wait Timer runs out.
3. When a DR fails and the BDR takes over, there is another election to see who becomes the replacement BDR.
4. The router sending the Hello packets with the highest priority wins the election.
5. If two or more routers tie with the highest priority setting, the router sending the Hello with the highest RID (Router ID) wins. NOTE: a RID is the highest logical (loopback) IP address configured on a router, if no logical/loopback IP address is set then the router uses the highest IP address configured on its active interfaces (e.g. 192.168.0.1 would be higher than 10.1.2.2).
6. Usually the router with the second-highest priority number becomes the BDR.
7. The priority values range between 0 – 255, with a higher value increasing its chances of becoming DR or BDR.
8. If a higher priority OSPF router comes online after the election has taken place, it will not become DR or BDR until (at least) the DR and BDR fail.
9. If the current DR 'goes down' the current BDR becomes the new DR and a new election takes place to find another BDR. If the new DR then 'goes down' and the original DR is now available, still previously chosen BDR will become DR.

### Routing Update Flow

**When DR has Routing update**

* DR sends LSU to 224.0.0.5
* BDR sends LSUAck to 224.0.0.5
* DR Other sends LSUAck to 224.0.0.6

**When BDR has Routing update**

* BDR sends LSU to 224.0.0.5
* DR sends LSUAck to 224.0.0.5
* DR Other sends LSUAck to 224.0.0.6

**When DR Other has Routing update**

* DR Other sends LSU to 224.0.0.6
* DR sends LSA to 224.0.0.5
* BDR sends LSUAck to 224.0.0.5
* Non-source routers, DR Other sends LSUAck to 224.0.0.6

## Protocol Messages

Unlike other routing protocols, OSPF does not carry data via a transport protocol, such as the User Datagram Protocol (UDP) or the Transmission Control Protocol (TCP). Instead, OSPF forms IP datagrams directly, packaging them using protocol number 89 for the IP Protocol field. OSPF defines five different message types, for various types of communication:

1. Hello
2. Database description
3. Link state request
4. Link state update
5. Link state acknowledgement

### Hello Packet

OSPF's Hello messages are used as a form of greeting, to allow a router to discover other adjacent routers on its local links and networks. The messages establish relationships between neighboring devices (called adjacencies) and communicate key parameters about how OSPF is to be used in the autonomous system or area. During normal operation, routers send hello messages to their neighbors at regular intervals (the hello interval); if a router stops receiving hello messages from a neighbor, after a set period (the dead interval) the router will assume the neighbor has gone down.

### Database description (DBD)

Database description messages contain descriptions of the topology of the autonomous system or area. They convey the contents of the link-state database (LSDB) for the area from one router to another. Communicating a large LSDB may require several messages to be sent by having the sending device designated as a leader device and sending messages in sequence, with the follower (recipient of the LSDB information) responding with acknowledgments.

### Link state packets

**Link state request (LSR)**
Link state request messages are used by one router to request updated information about a portion of the LSDB from another router. The message specifies the link(s) for which the requesting device wants more current information.

**Link state update (LSU)**
Link-state update messages contain updated information about the state of certain links on the LSDB. They are sent in response to a link state request message, and also broadcast or multicast by routers on a regular basis. Their contents are used to update the information in the LSDBs of routers that receive them.

**Link state acknowledgment (LSAck)**
Link-state acknowledgment messages provide reliability to the link-state exchange process, by explicitly acknowledging receipt of a Link State Update message.

### OSPF link state advertisements

| LS type | LS name | Generated by | Description |
| :--- | :--- | :--- | :--- |
| 1 | Router-LSAs | Each internal router within an area | The link-state ID of the type 1 LSA is the originating router ID. These describe types of interfaces like point-to-point connections, transit networks, stub networks (Reserved in v3), and virtual links. |
| 2 | Network-LSAs | The DR | Originated for broadcasts and NBMA networks by the designated router. This LSA contains the list of routers connected to the network. The link-state ID of the type 2 LSA is the IP interface address of the DR. |
| 3 | Summary-LSAs | The ABR | Type 3 summary-LSAs describe routes to networks. To inform other areas about inter-area routers, these routes can also be summarised. |
| 4 | ASBR-summary | The ABR | Type 4 describe routes to AS boundary routers beyond its area. The area border router (ABR) generates this LSA to inform other routers in the OSPF domain, that the matching router is an autonomous system boundary router (ASBR), so that the external LSAs (Type 5 / Type 7) it sent may be properly resolved outside its own area. |
| 5 | AS-external-LSAs | The ASBR | Type 5 These describe routes advertised by the ASBR. LSAs contain information imported into OSPF from other routing processes. Together with Type 4 they describe the way to an external route. |
| 7 | NSSA external link-state advertisements | The ASBR, within a not-so-stubby area | Type 7-LSAs are identical to type-5 LSAs. Type-7 LSAs are only flooded within the NSSA. At the area border router, selected type-7 LSAs are translated into type 5-LSAs and flooded into the backbone. |
| 8 | Link-LSA (v3) | Each internal router within a link | Provide it local router's link-local address to all other routers on the local network. |
| 9 | Intra-Area-Prefix-LSAs (v3) | Each internal router within an area | Replaces some of the functionality of Router-LSAs; stub network segment, or an attached transit network segment. |

## Routing metrics

OSPF uses path cost as its basic routing metric, which was defined by the standard not to equate to any standard value such as speed, so the network designer could pick a metric important to the design. In practice, it is determined by comparing the speed of the interface to a reference-bandwidth for the OSPF process. The cost is determined by dividing the reference bandwidth by the interface speed (although the cost for any interface can be manually overridden).

Metrics, however, are only directly comparable when of the same type. In decreasing preference, these types are:

1. Intra-area
2. Inter-area
3. External Type 1 (includes both the external path cost and the sum of internal path costs to the ASBR)
4. External Type 2 (the value of which is solely that of the external path cost)

## OSPF v3

OSPF version 3 introduces modifications to the IPv4 implementation of the protocol. Despite the expansion of addresses to 128 bits in IPv6, area and router Identifications are still 32-bit numbers.

**High-level changes:**

* Except for virtual links, all neighbor exchanges use IPv6 link-local addressing exclusively.
* The IPv6 protocol runs per link, rather than based on the subnet.
* All IP prefix information has been removed from the link-state advertisements and from the hello discovery packet, making OSPFv3 essentially protocol-independent.
* Three separate flooding scopes for LSAs: Link-local scope (flooded only on the local link), Area scope (flooded throughout a single OSPF area), and AS scope (flooded throughout the routing domain).
* Use of IPv6 link-local addresses for neighbor discovery, auto-configuration.
* Authentication has been moved to the IP Authentication Header.

**Changes introduced in OSPF v3, then backported by vendors to v2:**

* Explicit support for multiple instances per link.
* Packet format changes (Version number changed to 3; Options field removed from LSA header; Options field in hello/DBD changed from 16 to 24 bits; Interface ID added to hello packet).
* LSA format changes (Type field changed to 16 bits; Support for unknown types; Three bits for flooding scope; Addresses expressed as prefix and length; Address information removed from router-LSAs and network-LSAs; New Link-LSA type; Summary-LSAs renamed to inter-area-prefix/router-LSAs).

## OSPF over MPLS VPN

A customer can use OSPF over a MPLS VPN
