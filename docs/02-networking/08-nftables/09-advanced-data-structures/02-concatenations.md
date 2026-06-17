# Concatenations

Since Linux kernel 4.1, nftables supports concatenations. This new feature allows you to put two or more selectors together to perform very fast lookups in sets, maps, vmaps and meters.

## Anonymous sets

```bash
nft add rule ip filter input ip saddr . ip daddr . ip protocol { 1.1.1.1 . 2.2.2.2 . tcp, 1.1.1.1 . 3.3.3.3 . udp} counter accept
```

So if the packet's source IP address AND destination IP address AND level 4 protocol match:

1. 1.1.1.1 and 2.2.2.2 and TCP.
or
2. 1.1.1.1 and 3.3.3.3 and UDP.

nftables updates the counter for this rule and then accepts the packet.

## Named verdict maps

The following example creates the whitelist vmap using a concatenation of two selectors:

```bash
nft add map filter whitelist { type ipv4_addr . inet_service : verdict \; }
```

Once you create the vmap, you can use it from a rule that creates the following concatenation:

```bash
nft add rule filter input ip saddr . tcp dport vmap @whitelist
```

The rule above looks up a verdict based on the source IP address AND the TCP destination port. The verdict map is initially empty. You can dynamically populate it with elements:

```bash
nft add element filter whitelist { 1.2.3.4 . 22 : accept}
```

When declaring concatenations, you can use generic sets options, such as the `typeof` keyword and the counter feature:

```bash
table inet fmytable {
 set myset {
  typeof ip daddr . tcp dport
  counter
  elements = { 1.1.1.4 . 22 counter packets 0 bytes 0,
        1.1.1.5 . 23 counter packets 0 bytes 0,
        1.1.1.6 . 24 counter packets 0 bytes 0 }
 }
}
```

## Anonymous maps

The rule below determines the destination IP address that is used to perform DNAT to the packet based on:

- The source IP address
- AND
- The destination TCP port

```bash
nft add rule ip nat prerouting dnat to ip saddr . tcp dport map { 1.1.1.1 . 80 : 192.168.1.100, 2.2.2.2 . 8888 : 192.168.1.101 }
```

## Examples

Some concrete example concatenations so you get an idea on how powerful this new feature is.

### Network addresses

The example below implements a vmap using network masks in each element:

```bash
table inet mytable {
    set myset {
        type ipv4_addr . ipv4_addr
        flags interval
        elements = { 192.168.0.0/16 . 172.16.0.0/25,
                     10.0.0.0/30 . 192.168.1.0/24,
        }
    }

    chain mychain {
        ip saddr . ip daddr @myset counter accept
    }
}
```

**NOTE:** before Linux kernel 5.6 and nftables 0.9.4 the CIDR notation wasn't available, you would need to use a workaround:

```bash
nft add rule tablename chainname ip saddr and 255.255.255.0 . ip daddr and 255.255.255.0 vmap { 10.10.10.0 . 10.10.20.0 : accept }
```

Note that this is not an interval, this is masking the ip saddr and ip daddr, then concate both results. This concatenation is used to lookup for a matching of this the result in the map. This syntax may be compacted in future releases to support CIDR notation.

This could be easily implemented using a named map as well:

```bash
nft add map tablename myMap { type ipv4_addr . ipv4_addr : verdict \; }
nft add rule tablename chainname ip saddr and 255.255.255.0 . ip saddr and 255.255.255.0 vmap @myMap
nft add element tablename myMap { 10.10.10.0 . 10.10.20.0 : accept }
```

### Interfaces

The example below checks both input and output interfaces of a forwarded packet.

```bash
nft add rule tablename chainname iif . oif vmap { eth0 . eth1 : accept }
```

### Some ipset types

These ipset types can be implemented in nftables using concatenations. Probably more equivalences exists, it just a matter of combining data types. Of course, you could implement these as named maps/sets as well.
