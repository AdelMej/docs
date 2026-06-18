# Simple Network Management Protocol (SNMP)

Simple Network Management Protocol (SNMP) is an Internet Standard protocol used to collect and organize information about managed devices on IP networks and to modify that information to change device behavior. Devices that typically support SNMP include cable modems, routers, network switches, servers, workstations, printers, and more.

SNMP is widely used in network management for monitoring. It exposes management data in the form of variables on managed systems organized in a Management Information Base (MIB), which describes the system status and configuration. These variables can then be remotely queried (and, in some circumstances, manipulated) by managing applications.

Three significant versions of SNMP have been developed and deployed:

* **SNMPv1:** The original version of the protocol.
* **SNMPv2c:** Features improvements in performance, flexibility, and security.
* **SNMPv3:** Offers advanced security and remote configuration enhancements.

SNMP is a component of the Internet Protocol Suite as defined by the Internet Engineering Task Force (IETF). It consists of a set of standards for network management, including an application layer protocol, a database schema, and a set of data objects.

## Overview and Basic Concepts

### Principle of SNMP Communication

In typical uses of SNMP, one or more administrative computers called managers have the task of monitoring or managing a group of hosts or devices on a computer network. Each managed system executes a software component called an agent that reports information via SNMP to the manager.

An SNMP-managed network consists of three key components:

1. **Managed devices:** A network node that implements an SNMP interface allowing unidirectional (read-only) or bidirectional (read and write) access to node-specific information. Examples include routers, switches, cable modems, IP telephones, cameras, and printers.
2. **Agent:** A network-management software module residing on a managed device. It has local knowledge of management information and translates it into an SNMP-specific form.
3. **Network Management Station (NMS):** Software running on the manager that executes applications to monitor and control managed devices. NMSs provide most of the processing and memory resources required for network management.

### Management Information Base (MIB)

SNMP agents expose management data as variables. The protocol permits active management tasks, such as configuration changes, through remote modification of these variables. These variables are organized in hierarchies described as a Management Information Base (MIB). MIBs describe the structure of the management data of a device subsystem using a hierarchical namespace containing object identifiers (OID). Each OID identifies a variable that can be read or set via SNMP. MIBs use the notation defined by Structure of Management Information Version 2.0 (SMIv2, RFC 2578), a subset of ASN.1.

## Protocol Details

SNMP operates in the application layer of the Internet protocol suite. All SNMP messages are transported via User Datagram Protocol (UDP).

* **Agent Port:** The agent receives requests on UDP port 161.
* **Manager Port:** The manager may send requests from any source port to port 161.
* **Notification Port:** The manager receives notifications (Traps and InformRequests) on port 162.
* **Secure Ports:** When used with Transport Layer Security or Datagram Transport Layer Security, requests are received on port 10161 and notifications are sent to port 10162.

### SNMP Protocol Data Units (PDUs)

SNMPv1 specifies five core protocol data units (PDUs). GetBulkRequest and InformRequest were added in SNMPv2, and the Report PDU was added in SNMPv3. All SNMP PDUs are constructed using an IP header, a UDP header, version, community, PDU-type, request-id, error-status, error-index, and variable bindings.

The seven primary SNMP PDU types are:

* **GetRequest:** A manager-to-agent request to retrieve the value of one or more variables.
* **SetRequest:** A manager-to-agent request to change the value of one or more variables.
* **GetNextRequest:** A manager-to-agent request to discover available variables and their values by returning the lexicographically next variable in the MIB.
* **GetBulkRequest:** An optimized version of GetNextRequest for retrieving large amounts of management data in a single request (introduced in SNMPv2).
* **Response:** Returns variable bindings and acknowledgement from the agent to the manager.
* **Trap:** An asynchronous notification sent from an agent to a manager without being explicitly requested, notifying the station of significant events.
* **InformRequest:** An acknowledged asynchronous notification. Unlike a Trap, an InformRequest requires the manager to return an acknowledgement, ensuring delivery over UDP.

## Protocol Versions

### Version 1 (SNMPv1)

SNMPv1 was developed in the 1980s as an interim protocol for large-scale deployment of the Internet. It is widely used and remains a de facto network management protocol. However, it has been criticized for poor security because community names (passwords) are transmitted in cleartext.

### Version 2 (SNMPv2c)

SNMPv2 introduced GetBulkRequest and improved performance. SNMPv2c is the most widely used version of v2; it uses the simple community-based security scheme of SNMPv1 rather than the more complex (and less adopted) party-based security model of original SNMPv2.

### Version 3 (SNMPv3)

SNMPv3 adds cryptographic security and remote configuration enhancements. It defines:

* **SNMPEngineID:** Unique identifiers for SNMP entities to ensure communication only between known peers.
* **User-based Security Model (USM):** Provides strong authentication and data encryption (privacy).
* **View-based Access Control Model (VACM):** Determines which principals are allowed access to specific MIB objects.
* **Security Goals:** Protects against modification of information, masquerading, message stream modification, and disclosure (eavesdropping).
* **Authentication and Privacy Protocols:** Supports MD5, SHA, and HMAC-SHA-2 for authentication, and CBC_DES or CFB_AES_128 for privacy.

## Security Implications

### Vulnerabilities

SNMP can be used to penetrate a network if read-write modes are misconfigured. Mistakes in configuration can make a network susceptible to attacks. Furthermore, even in read-only mode, certain implementations have been found vulnerable to denial of service attacks.

### Authentication

* **SNMPv1 and v2c:** Use community strings (passwords) sent in plaintext. If the network is not otherwise secured, these can be intercepted via packet sniffing.
* **SNMPv3:** Uses cryptographic keys for authentication and encryption. It supports various security levels: NoAuthNoPriv (no authentication, no privacy), AuthNoPriv (authentication but no privacy), and AuthPriv (both authentication and privacy).
* **Spoofing:** SNMPv1 and v2c are vulnerable to IP spoofing. SNMPv3 security mechanisms (USM or TSM) can prevent these attacks.
