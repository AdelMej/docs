# Maps

An nftables map stores key-value pairs, similar to associative arrays, dictionaries, or hashes in many programming languages. In an nftables rule, you can specify a packet field (e.g., `tcp dport`) and reference a map to search for the map element whose key matches the packet field's value, and return that map element's value (or failure if the map contains no matching element).

You can think of a map as a set that returns a value instead of just an "in set / not in set" result. Behind the scenes, both sets and maps use the same generic set infrastructure and therefore share many of the same options and semantics.

## Anonymous maps

The following rule uses a map to DNAT packets to different destination IP addresses depending on the packet's destination TCP port:

```bash
% nft add rule ip nat prerouting dnat to tcp dport map { 80 : 192.168.1.100, 8888 : 192.168.1.101 }
```

This is a very concise and efficient way to perform classical port redirection when the real servers are located behind a firewall. It functions as follows:

* If the TCP destination port is 80, then the packet is DNAT'ed to 192.168.1.100.
* If the TCP destination port is 8888, then the packet is DNAT'ed to 192.168.1.101.

## Named maps

You can also declare named maps, to which you can then add or delete elements at any time. For example:

```bash
% nft add map nat porttoip  { type inet_service: ipv4_addr\; }
% nft add element nat porttoip { 80 : 192.168.1.100, 8888 : 192.168.1.101 }
```

In this example, we first define a map `porttoip` which looks up an IP address based on a UDP or TCP port number, and then add a couple of elements. The map itself only cares about the data types of its keys and values; it is up to the user to use meaningful values for both and to use the map appropriately in rules. In this example, we use `porttoip` to map TCP destination ports to source IP addresses. We can then perform SNAT using the following rule:

```bash
% nft add rule ip nat postrouting snat to tcp dport map @porttoip
```

Outgoing packets to TCP/80 use source IP address 192.168.1.100, while those to TCP/8888 are SNAT'ed to 192.168.1.101. We can easily adjust our SNAT at any time simply by adding or deleting elements in `porttoip`.
