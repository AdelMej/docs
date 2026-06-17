# Matching routing information

Starting with linux 4.10 and nftables v0.7, there are new mechanisms to match several routing information related to packets and the firewall machine.

## nexthop

The directly connected IP address that an outgoing packet is sent to, which can be used either for matching or accounting, eg:

```bash
nft add rule filter postrouting ip daddr 192.168.1.0/24 rt nexthop != 192.168.0.1 drop
```

This will drop any traffic to 192.168.1.0/24 that is not routed via 192.168.0.1.

```bash
nft add rule filter postrouting meter acct { rt nexthop timeout 600s counter }
nft add rule ip6 filter postrouting meter acct { rt nexthop timeout 600s counter }
```

These rules count outgoing traffic per nexthop. Note that the timeout releases an entry if no traffic is seen for this nexthop within 10 minutes.

**General syntax is:** `rt key operator expression`, where:

* **key:** classid, nexthop
* **operator:** eq, neq, gt, lt, gte, lte, vmap, map

## fib

The fib statement can be used to obtain the output interface from the route table based on either source or destination address of a packet. This can be used to e.g. add reverse path filtering, or eg. drop if not coming from the same interface packet arrived on:

```bash
nft add rule x prerouting fib saddr . iif oif eq 0 drop
```

Accept only if from eth0:

```bash
nft add rule x prerouting fib saddr . iif oif eq "eth0" accept
```

Accept if from any valid interface:

```bash
nft add rule x prerouting fib saddr oif accept
```

Querying of address type is also supported, this can be used to only accept packets to addresses configured in the same interface, eg:

```bash
nft add rule x prerouting fib daddr . iif type local accept
```

Its also possible to use verdict maps, eg:

```bash
nft 'add rule x prerouting fib saddr . mark oif vmap { "eth0" : drop, "ppp0" : accept }'
```

This takes the packet source address and queries the routing table for the output interface index that would be used to send a packet to that address. The packets destination address is used as source address. The ". mark" syntax tells the fib expression it should also consider the packet mark when the fib is queried. Likewise, "iif" and "oif" tell fib to consider the packets in and output interface on lookup.

**General syntax is:** `fib key data operator expression`, where:

* **key:** saddr, daddr, mark, iif, oif (use '.' for concatenations to represent tuples)
* **data:** oif, oifname, (address) type
* **operator:** eq, neq, vmap, map
