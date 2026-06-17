# OSI model

The Open Systems Interconnection (OSI) model is a reference model developed by the International Organization for Standardization (ISO) that provides a common basis for the coordination of standards development for the purpose of systems interconnection.

In the OSI reference model, the components of a communication system are distinguished in seven abstraction layers: Physical, Data Link, Network, Transport, Session, Presentation, and Application. The model describes communications from the physical implementation of transmitting bits across a transmission medium to the highest-level representation of data of a distributed application. Each layer has well-defined functions and semantics and serves a class of functionality to the layer above it and is served by the layer below it. Established, well-known communication protocols are decomposed in software development into the model's hierarchy of function calls.

The Internet protocol suite as defined in RFC 1122 and RFC 1123 is a model of networking developed contemporarily to the OSI model, and was funded primarily by the U.S. Department of Defense. It was the foundation for the development of the Internet. It assumed the presence of generic physical links and focused primarily on the software layers of communication, with a similar but much less rigorous structure than the OSI model.

In comparison, several networking models have sought to create an intellectual framework for clarifying networking concepts and activities, but none have been as successful as the OSI reference model in becoming the standard model for discussing and teaching networking in the field of information technology. The model allows transparent communication through equivalent exchange of protocol data units (PDUs) between two parties, through what is known as peer-to-peer networking (also known as peer-to-peer communication). As a result, the OSI reference model has not only become an important piece among professionals and non-professionals alike, but also in all networking between one or many parties, due in large part to its commonly accepted user-friendly framework.

## Layer Summary

| Layer | Protocols / Examples |
| :--- | :--- |
| **7. Application layer** | NNTP, SIP, SSI, DNS, FTP, Gopher, HTTP (HTTP/3), NFS, NTP, SMPP, SSH, SMTP, SNMP, Telnet, DHCP, NETCONF |
| **6. Presentation layer** | MIME, XDR, ASN.1, ASCII, TLS, PGP |
| **5. Session layer** | Named pipe, NetBIOS, SAP, PPTP, RTP, SOCKS, X.225 |
| **4. Transport layer** | TCP, UDP, SCTP, DCCP, QUIC, SPX |
| **3. Network layer** | IP, IPv4, IPv6, ICMP (ICMPv6), IPsec, IGMP, IPX, IS-IS, AppleTalk, X.25 PLP |
| **2. Data link layer** | ATM, ARP, SDLC, HDLC, CSLIP, SLIP, GFP, PLIP, IEEE 802, LLC, MAC, L2TP, Frame Relay, ITU-T G.hn DLL, PPP, X.25 LAPB, Q.922 LAPF |
| **1. Physical layer** | RS-232, RS-449, ITU-T V-Series, I.430, I.431, PDH, SONET/SDH, PON, OTN, DSL, IEEE 802, IEEE 1394, ITU-T G.hn PHY, USB, Bluetooth, X.21 |

## History

The development of the OSI model started in the late 1970s to support the emergence of the diverse computer networking methods that were competing for application in the large national networking efforts in the world. In the 1980s, the model became a working product of the Open Systems Interconnection group at the International Organization for Standardization (ISO). While attempting to provide a comprehensive description of networking, the model failed to garner reliance during the design of the Internet, which is reflected in the less prescriptive Internet Protocol Suite, principally sponsored under the auspices of the Internet Engineering Task Force (IETF).

In the early- and mid-1970s, networking was largely either government-sponsored or vendor-developed with proprietary standards. Public data networks began to use the X.25 standard in the late 1970s. The Experimental Packet Switched System in the UK identified the need for defining higher-level protocols.

The OSI model was first defined in raw form in Washington, D.C., in February 1978 by French software engineer Hubert Zimmermann, and the refined but still draft standard was published by the ISO in 1980. In May 1983, the CCITT and ISO documents were merged to form The Basic Reference Model for Open Systems Interconnection. It was published in 1984 by both the ISO as standard ISO 7498, and the renamed CCITT (now called the Telecommunications Standardization Sector of the International Telecommunication Union or ITU-T) as standard X.200.

## Definitions

Communication protocols enable an entity in one host to interact with a corresponding entity at the same layer in another host. Service definitions, like the OSI model, abstractly describe the functionality provided to a layer N by a layer N−1, where N is one of the seven layers of protocols operating in the local host (with N=1 being the most basic layer).

At each level N, two entities at the communicating devices (layer N peers) exchange protocol data units (PDUs) by means of a layer N protocol. Each PDU contains a payload, called the service data unit (SDU), along with protocol-related headers or footers.

Data processing proceeds as follows:

1. The data to be transmitted is composed at the topmost layer of the transmitting device (layer N) into a protocol data unit (PDU).
2. The PDU is passed to layer N−1, where it is known as the service data unit (SDU).
3. At layer N−1 the SDU is concatenated with a header, a footer, or both, producing a layer N−1 PDU. It is then passed to layer N−2.
4. The process continues until reaching the lowermost level, from which the data is transmitted to the receiving device.
5. At the receiving device the data is passed from the lowest to the highest layer as a series of SDUs while being successively stripped from each layer's header or footer until reaching the topmost layer, where the last of the data is consumed.

## Standards documents

The OSI model was defined in ISO/IEC 7498 which consists of the following parts:

- ISO/IEC 7498-1 The Basic Model
- ISO/IEC 7498-2 Security Architecture
- ISO/IEC 7498-3 Naming and addressing
- ISO/IEC 7498-4 Management framework

ISO/IEC 7498-1 is also published as ITU-T Recommendation X.200.

## Layer architecture

The recommendation X.200 describes seven layers, labelled 1 to 7. Layer 1 is the lowest layer in this model.

| Layer | Protocol data unit (PDU) | Function |
| :--- | :--- | :--- |
| **7. Application** | Data | High-level protocols such as for resource sharing or remote file access, e.g. HTTP. |
| **6. Presentation** | | Translation of data between a networking service and an application; including character encoding, data compression and encryption/decryption |
| **5. Session** | | Managing communication sessions, i.e., continuous exchange of information in the form of multiple back-and-forth transmissions between two nodes |
| **4. Transport** | Segment | Reliable transmission of data segments between points on a network, including segmentation, acknowledgement and multiplexing |
| **3. Network** | Packet, Datagram | Structuring and managing a multi-node network, including addressing, routing and traffic control |
| **2. Data link** | Frame | Transmission of data frames between two nodes connected by a physical layer |
| **1. Physical** | Bit, Symbol | Transmission and reception of raw bit streams over a physical medium |

### Layer 1: Physical layer

The physical layer is responsible for the transmission and reception of unstructured raw data between a device, such as a network interface controller, Ethernet hub, or network switch, and a physical transmission medium. It converts the digital bits into electrical, radio, or optical signals (analogue signals). Layer specifications define characteristics such as voltage levels, timing of voltage changes, physical data rates, maximum transmission distances, modulation scheme, channel access method and physical connectors.

### Layer 2: Data link layer

The data link layer provides node-to-node data transfer—a link between two directly connected nodes. It detects and possibly corrects errors that may occur in the physical layer. It defines the protocol to establish and terminate a connection between two physically connected devices and the protocol for flow control between them. IEEE 802 divides the data link layer into two sublayers: Medium access control (MAC) layer and Logical link control (LLC) layer.

### Layer 3: Network layer

The network layer provides the functional and procedural means of transferring packets from one node to another connected in "different networks". A network is a medium to which many nodes can be connected, on which every node has an address and which permits nodes connected to it to transfer messages to other nodes connected to it by merely providing the content of a message and the address of the destination node and letting the network find the way to deliver the message to the destination node.

### Layer 4: Transport layer

The transport layer provides the functional and procedural means of transferring variable-length data sequences from a source host to a destination host from one application to another across a network while maintaining the quality-of-service functions. Transport protocols may be connection-oriented or connectionless. It also controls the reliability of a given link between a source and destination host through flow control, error control, and acknowledgments.

### Layer 5: Session layer

The session layer creates the setup, controls the connections, and ends the teardown, between two or more computers, which is called a "session". Common functions include user logon (establishment) and user logoff (termination) functions. The session layer also provides for full-duplex, half-duplex, or simplex operation and establishes procedures for checkpointing, suspending, restarting, and terminating a session.

### Layer 6: Presentation layer

The presentation layer establishes data formatting and data translation into a format specified by the application layer during the encapsulation of outgoing messages while being passed down the protocol stack, and possibly reversed during the deencapsulation of incoming messages when being passed up the protocol stack. It handles protocol conversion, data encryption, data decryption, data compression, data decompression, incompatibility of data representation between operating systems, and graphic commands.

### Layer 7: Application layer

The application layer is the layer of the OSI model that is closest to the end user. Both the OSI application layer and the user interact directly with a software application that implements a component of communication between the client and server. Application-layer functions typically include file sharing, message handling, and database access, through the most common protocols known as HTTP, FTP, SMB/CIFS, TFTP, and SMTP.

## Cross-layer functions

Cross-layer functions are services that are not tied to a given layer, but may affect more than one layer. Some orthogonal aspects, such as management and security, involve all of the layers. These services are aimed at improving the CIA triad—confidentiality, integrity, and availability—of the transmitted data.

Specific examples of cross-layer functions include:

- Security service (telecommunication) as defined by ITU-T X.800 recommendation.
- Management functions, i.e. functions that permit to configure, instantiate, monitor, terminate the communications of two or more entities.
- Multiprotocol Label Switching (MPLS), ATM, and X.25 are 3a protocols. OSI subdivides the Network Layer into three sublayers: 3a) Subnetwork Access, 3b) Subnetwork Dependent Convergence and 3c) Subnetwork Independent Convergence.
- Cross MAC and PHY Scheduling is essential in wireless networks.

## Programming interfaces

Neither the OSI Reference Model, nor any OSI protocol specifications, outline any programming interfaces, other than deliberately abstract service descriptions. Protocol specifications define a methodology for communication between peers, but the software interfaces are implementation-specific.

## Comparison to other networking suites

The TCP/IP model of the Internet does not concern itself with strict hierarchical encapsulation and layering. Despite using a different concept for layering than the OSI model, these layers are often compared with the OSI layering scheme in the following manner:

- The Internet application layer maps to the OSI application layer, presentation layer, and most of the session layer.
- The TCP/IP transport layer maps to the graceful close function of the OSI session layer as well as the OSI transport layer.
- The internet layer performs functions as those in a subset of the OSI network layer.
- The link layer corresponds to the OSI data link layer and may include similar functions as the physical layer, as well as some protocols of the OSI's network layer.
