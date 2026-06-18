# Precision Time Protocol

The Precision Time Protocol (PTP) is a protocol for clock synchronization throughout a computer network with relatively high precision as compared to using the earlier developed Network Time Protocol (NTP) and therefore potentially higher accuracy depending on the configuration. In a local area network (LAN), accuracy can be sub-microsecond – making it suitable for measurement and control systems applications. PTP can be used to synchronize financial transactions, mobile phone tower transmissions, sub-sea acoustic arrays, and networks that require precise timing as an alternative to using the timestamp of satellite navigation signals or where sub-nanosecond accuracy as provided by the White Rabbit Project is unnecessary.

The first version of PTP, IEEE 1588-2002, was published in 2002. IEEE 1588-2008, also known as PTP Version 2, is not backward compatible with the 2002 version. IEEE 1588-2019 was published in November 2019 and includes backward-compatible improvements to the 2008 publication. IEEE 1588-2008 includes a profile concept defining PTP operating parameters and options. Several profiles have been defined for applications including telecommunications, electric power distribution and audiovisual uses. IEEE 802.1AS is an adaptation of PTP, called gPTP, for use with Audio Video Bridging (AVB) and Time-Sensitive Networking (TSN).

## History

IEEE 1588 is designed to fill a niche not well served by either of the two dominant protocols, NTP and GPS. IEEE 1588 is designed for local systems requiring accuracies beyond those attainable using NTP. It is also designed for applications that cannot bear the cost of a GPS receiver at each node, or for which GPS signals are inaccessible.

PTP was originally defined in the IEEE 1588-2002 standard, officially titled Standard for a Precision Clock Synchronization Protocol for Networked Measurement and Control Systems, and published in 2002. In 2008, IEEE 1588-2008 was released as a revised standard; also known as PTP version 2 (PTPv2), it improves accuracy, precision and robustness but is not backward compatible with the original 2002 version. IEEE 1588-2019 was published in November 2019, is informally known as PTPv2.1 and includes backwards-compatible improvements to the 2008 publication.

## Architecture

The IEEE 1588 standards describe a hierarchical master–slave architecture for clock distribution consisting of one or more network segments and one or more clocks. An ordinary clock is a device with a single network connection that is either the source of or the destination for a synchronization reference. A source is called a master (alternately timeTransmitter), and a destination is called a slave (alternately timeReceiver). A boundary clock has multiple network connections and synchronizes one network segment to another. A single, synchronization leader is selected, a.k.a. elected, for each network segment. The root timing reference is called the grandmaster.

A relatively simple PTP architecture consists of ordinary clocks on a single-segment network with no boundary clocks. A grandmaster is elected and all other clocks synchronize to it.

IEEE 1588-2008 introduces a clock associated with network equipment used to convey PTP messages. The transparent clock modifies PTP messages as they pass through the device. Timestamps in the messages are corrected for time spent traversing the network equipment. This scheme improves distribution accuracy by compensating for delivery variability across the network.

PTP typically uses the same epoch as Unix time (start of 1 January 1970). While the Unix time is based on Coordinated Universal Time (UTC) and is subject to leap seconds, PTP is based on International Atomic Time (TAI). The PTP grandmaster communicates the current offset between UTC and TAI, so that UTC can be computed from the received PTP time.

## Protocol details

Synchronization and management of a PTP system is achieved through the exchange of messages across the communications medium. To this end, PTP uses the following message types:

* **Sync, Follow_Up, Delay_Req and Delay_Resp messages:** Used by ordinary and boundary clocks and communicate time-related information used to synchronize clocks across the network.
* **Pdelay_Req, Pdelay_Resp and Pdelay_Resp_Follow_Up:** Used by transparent clocks to measure delays across the communications medium so that they can be compensated for by the system. Transparent clocks and these messages associated with them are not available in the original IEEE 1588-2002 PTPv1 standard, and were added in PTPv2.
* **Announce messages:** Used by the best master clock algorithm in IEEE 1588-2008 to build a clock hierarchy and select the grandmaster.
* **Management messages:** Used by network management to monitor, configure and maintain a PTP system.
* **Signaling messages:** Used for non-time-critical communications between clocks. Signaling messages were introduced in IEEE 1588-2008.

Messages are categorized as event and general messages. Event messages are time-critical in that accuracy in transmission and receipt timestamp accuracy directly affects clock distribution accuracy. Sync, Delay_Req, Pdelay_Req and Pdelay_resp are event messages. General messages are more conventional protocol data units in that the data in these messages is of importance to PTP, but their transmission and receipt timestamps are not. Announce, Follow_Up, Delay_Resp, Pdelay_Resp_Follow_Up, Management and Signaling messages are members of the general message class.

## Message transport

PTP messages may use the User Datagram Protocol over Internet Protocol (UDP/IP) for transport. IEEE 1588-2002 uses only IPv4 transports, but this has been extended to include IPv6 in IEEE 1588-2008. In IEEE 1588-2002, all PTP messages are sent using multicast messaging, while IEEE 1588-2008 introduced an option for devices to negotiate unicast transmission on a port-by-port basis. Multicast transmissions use IP multicast addressing, for which multicast group addresses are defined for IPv4 and IPv6.

Time-critical event messages (Sync, Delay_req, Pdelay_Req and Pdelay_Resp) are sent to port number 319. General messages (Announce, Follow_Up, Delay_Resp, Pdelay_Resp_Follow_Up, management and signaling) use port number 320.

### Multicast group addresses

| Messages | IPv4 | IPv6 | IEEE 802.3 Ethernet |
| :--- | :--- | :--- | :--- |
| All except peer delay messages | 224.0.1.129 | FF0x::181 | 01-1B-19-00-00-00 |
| Forwardable Peer delay messages: Pdelay_Req, Pdelay_Resp and Pdelay_Resp_Follow_Up | 224.0.0.107 | FF02::6B | 01-80-C2-00-00-0E |
| Non-forwardable | | | |

In IEEE 1588-2008, encapsulation is also defined for DeviceNet, ControlNet and PROFINET.

## Domains

A domain is an interacting set of clocks that synchronize to one another using PTP. Clocks are assigned to a domain by virtue of the contents of the Subdomain name (IEEE 1588-2002) or the domainNumber (IEEE 1588-2008) fields in PTP messages they receive or generate. Domains allow multiple clock distribution systems to share the same communications medium.

### Subdomain name field contents (IEEE1588-2002)

| Subdomain name | IPv4 multicast address | domainNumber (IEEE1588-2008) |
| :--- | :--- | :--- |
| _DFLT | 224.0.1.129 | 0 |
| _ALT1 | 224.0.1.130 | 1 |
| _ALT2 | 224.0.1.131 | 2 |
| _ALT3 | 224.0.1.132 | 3 |
| Application specific up to 15 octets | 224.0.1.130, 131 or 132 as per hash function on Subdomain name | 4 through 127 |
| User-defined domains | | |

## Best master clock algorithm

The best master clock algorithm (BMCA) performs a distributed selection of the best clock to act as leader based on the following clock properties:

* **Identifier:** A universally unique numeric identifier for the clock. This is typically constructed based on a device's MAC address.
* **Quality:** Both versions of IEEE 1588 attempt to quantify clock quality based on expected timing deviation, technology used to implement the clock or location in a clock stratum schema, although only V1 (IEEE 1588-2002) knows a data field stratum. PTP V2 (IEEE 1588-2008) defines the overall quality of a clock by using the data fields clockAccuracy and clockClass.
* **Priority:** An administratively assigned precedence hint used by the BMCA to help select a grandmaster for the PTP domain. IEEE 1588-2002 used a single Boolean variable to indicate precedence. IEEE 1588-2008 features two 8-bit priority fields.
* **Variance:** A clock's estimate of its stability based on observation of its performance against the PTP reference.

IEEE 1588-2008 uses a hierarchical selection algorithm based on the following properties, in the indicated order:

1. **Priority 1:** The user can assign a specific static-designed priority to each clock, preemptively defining a priority among them. Smaller numeric values indicate higher priority.
2. **Class:** Each clock is a member of a given class, each class getting its own priority.
3. **Accuracy:** Precision between clock and UTC, in nanoseconds (ns).
4. **Variance:** Variability of the clock.
5. **Priority 2:** Final-defined priority, defining backup order in case the other criteria were not sufficient. Smaller numeric values indicate higher priority.
6. **Unique identifier:** MAC address-based selection is used as a tiebreaker when all other properties are equal.

Clock properties are advertised in IEEE 1588-2002 Sync messages and in IEEE 1588-2008 Announce messages. The current leader transmits this information at regular interval. A clock that considers itself a better leader will transmit this information in order to invoke a change of leader. Once the current leader recognizes the better clock, the current leader stops transmitting Sync messages and associated clock properties (Announce messages in the case of IEEE 1588-2008) and the better clock takes over as leader. The BMCA only considers the self-declared quality of clocks and does not take network link quality into consideration.

## Synchronization

Via BMCA, PTP selects a source of time for an IEEE 1588 domain and for each network segment in the domain. Clocks determine the offset between themselves and their leader. Let the variable $t$ represent physical time. For a given follower device, the offset $o(t)$ at time $t$ is defined by:

$$o(t) = s(t) - m(t)$$

where $s(t)$ represents the time measured by the follower clock at physical time $t$, and $m(t)$ represents the time measured by the leader clock at physical time $t$.

The leader periodically broadcasts the current time as a message to the other clocks. Under IEEE 1588-2002 broadcasts are up to once per second. Under IEEE 1588-2008, up to 10 per second are permitted.

### IEEE 1588 synchronization mechanism and delay calculation

Each broadcast begins at time $T_1$ with a Sync message sent by the leader to all the clocks in the domain. A clock receiving this message takes note of the local time $T_1'$ when this message is received.

The leader may subsequently send a multicast Follow_Up with accurate $T_1$ timestamp. Not all leaders have the ability to present an accurate timestamp in the Sync message. It is only after the transmission is complete that they are able to retrieve an accurate timestamp for the Sync transmission from their network hardware. Leaders with this limitation use the Follow_Up message to convey $T_1$. Leaders with PTP capabilities built into their network hardware are able to present an accurate timestamp in the Sync message and do not need to send Follow_Up messages.

In order to accurately synchronize to their leader, clocks must individually determine the network transit time of the Sync messages. The transit time is determined indirectly by measuring round-trip time from each clock to its leader. The clocks initiate an exchange with their leader designed to measure the transit time $d$. The exchange begins with a clock sending a Delay_Req message at time $T_2$ to the leader. The leader receives and timestamps the Delay_Req at time $T_2'$ and responds with a Delay_Resp message. The leader includes the timestamp $T_2'$ in the Delay_Resp message.

Through these exchanges a clock learns $T_1$, $T_1'$, $T_2$ and $T_2'$. If $d$ is the transit time for the Sync message, and $\tilde{o}$ is the constant offset between leader and follower clocks, then:

$$T_1' - T_1 = \tilde{o} + d$$
$$T_2' - T_2 = -\tilde{o} + d$$

Combining the above two equations, we find that:

$$\tilde{o} = \frac{1}{2}(T_1' - T_1 - T_2' + T_2)$$

The clock now knows the offset $\tilde{o}$ during this transaction and can correct itself by this amount to bring it into agreement with their leader.

One assumption is that this exchange of messages happens over a period of time so small that this offset can safely be considered constant over that period. Another assumption is that the transit time of a message going from the leader to a follower is equal to the transit time of a message going from the follower to the leader. Finally, it is assumed that both the leader and follower can accurately measure the time they send or receive a message. The degree to which these assumptions hold true determines the accuracy of the clock at the follower device.

## Optional features

IEEE 1588-2008 standard lists the following set of features that implementations may choose to support:

* Alternate Time-Scale
* Grand Master Cluster
* Unicast Masters
* Alternate Master
* Path Trace

IEEE 1588-2019 adds additional optional and backward-compatible features:

* Modular transparent clocks
* Special PTP ports to interface with transports with built-in time distribution
* Unicast Delay_Req and Delay_Resp messages
* Manual port configuration overriding BMCA
* Asymmetry calibration
* Ability to utilize a physical layer frequency reference (e.g. Synchronous Ethernet)
* Profile isolation
* Inter-domain interactions
* Security TLV for integrity checking
* Standard performance reporting metrics
* Slave port monitoring

## Related initiatives

* The International IEEE Symposium on Precision Clock Synchronization for Measurement, Control and Communication (ISPCS) is an IEEE-organized annual event.
* The Institute of Embedded Systems (InES) of the Zurich University of Applied Sciences/ZHAW is addressing the practical implementation and application of PTP.
* IEEE 1588 is a key technology in the LXI Standard for Test and Measurement communication and control.
* IEEE 802.1AS-2011 is part of the IEEE Audio Video Bridging (AVB) group of standards. It specifies a profile for the use of IEEE 1588-2008 for time synchronization over a virtual bridged local area network as defined by IEEE 802.1Q. In particular, 802.1AS defines how IEEE 802.3 (Ethernet), IEEE 802.11 (Wi-Fi), and MoCA can all be parts of the same PTP timing domain.
* SMPTE 2059-2 is a PTP profile for use in synchronization of broadcast media systems.
* The AES67 audio networking interoperability standard includes a PTPv2 profile compatible with SMPTE ST2059-2.
* Dante uses PTPv1 for synchronization.
* Q-LAN and RAVENNA use PTPv2 for time synchronization.
* The White Rabbit Project combines Synchronous Ethernet and PTP.
* Precision Time Protocol Industry Profile PTP profiles (L2P2P and L3E2E) for industrial automation in IEC 62439-3.
* IEC/IEEE 61850-9-3 PTP profile for substation automation adopted by IEC 61850.
* Parallel Redundancy Protocol use of PTP profiles (L2P2P and L3E2E) for industrial automation in parallel networks.
* PTP is being studied to be applied as a secure time synchronization protocol in power systems' Wide Area Monitoring.
