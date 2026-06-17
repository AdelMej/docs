# Nftables Data Types Documentation

Nftables provides a variety of data types for filtering and packet handling. The `nft describe` command can be used to retrieve detailed information about specific data types, identify the data type of a selector, or list predefined symbolic constants.

## Date and Time Types

These types allow for filtering based on when a packet is received.

- **day**: An 8-bit integer representing the day of the week (0 = Sunday, 6 = Saturday).
- **hour**: A 32-bit integer representing the hour of the day, specified in `hh:mm[:ss]` format.
- **time**: A 64-bit integer representing the relative time of packet reception. It accepts ISO format strings (e.g., "2019-06-06") or UNIX timestamps.

## Network Interface Types

Types used to identify and filter by network interfaces.

- **devgroup**: A 32-bit integer representing a device group.
- **iface_index**: A 32-bit integer representing the interface index; can be specified numerically or by name.
- **iface_type**: A 16-bit integer representing the interface type (e.g., ether, ppp, ipip, loopback, etc.).
- **ifkind**: A 16-byte string representing the interface kind name.
- **ifname**: A 16-byte string representing the interface name. While slower than using an index, it is useful for interfaces that appear and disappear dynamically.

## Ethernet Types

- **ether_addr**: A 48-bit integer representing the Ethernet address.
- **ether_type**: A 16-bit integer representing the EtherType (e.g., arp, ip, ip6, vlan). Note that nftables uses little-endian order on x86.

## ARP Types

These types handle Address Resolution Protocol traffic.

- **HLEN**: Hardware address length in octets (8-bit integer).
- **HTYPE**: Hardware type (16-bit integer).
- **PLEN**: Internetwork address length in octets (8-bit integer).
- **arp_op**: A 16-bit integer representing the ARP operation (e.g., request, reply, nak).

## IP Types

Standard types for handling Internet Protocol traffic.

- **inet_proto**: An 8-bit integer representing the Internet protocol (e.g., tcp, udp, icmp, sctp).
- **inet_service**: A 16-bit integer representing the network service port number.
- **ipv4_addr**: A 32-bit integer representing an IPv4 address.
- **ipv6_addr**: A 128-bit integer representing an IPv6 address.

## Conntrack Types

Types related to connection tracking.

- **ct_dir**: An 8-bit integer for conntrack direction (original or reply).
- **ct_event**: A 4-byte bitmask representing conntrack event bits (e.g., new, related, destroy, helper, mark).
- **ct_label**: A 128-bit bitmask for the conntrack label.
- **ct_state**: A 4-byte bitmask for conntrack states (e.g., invalid, established, related, new, untracked).
- **ct_status**: A 4-byte bitmask for conntrack status (e.g., expected, seen-reply, snat, dnat, dying).

## Other Types

- **gid**: A 32-bit integer representing the Group ID.
- **mark**: A 32-bit integer used as a packet mark.
- **pkt_type**: An 8-bit integer representing the packet type (host/unicast, broadcast, multicast, or other).
- **realm**: A 32-bit integer representing the Routing Realm.
- **uid**: A 32-bit integer representing the User ID.
