# Mangling packet headers

Since nft v0.6, nftables supports packet header mangling, including stateless NAT. Note: if you mangle packet fields that are included in the layer 4 checksum pseudoheader, then you require a Linux kernel version >= 4.10.

To mangle packet header fields you should create a rule to match the packet, match the desired header field and set a new value to it:

```bash
% nft add table raw
% nft add chain raw prerouting {type filter hook prerouting priority -300\;}
% nft add rule raw prerouting tcp dport 8080 tcp dport set 80
```

The commands above create a table named `raw`, a chain named `prerouting` (see Netfilter hooks), and a rule to mangle the destination port of packets over TCP from 8080 to 80.

## Mangling TCP options

Since Linux kernel 4.14 and nftables 0.9, you can clamp your TCP MSS to Path MTU. This is very convenient in case your router encapsulates traffic over PPPoE, which is what many DSL (and some FTTH) providers do:

```bash
nft add rule ip filter forward tcp flags syn tcp option maxseg size set rt mtu
```

where `rt mtu` calculates the MTU in runtime based on what the routing cache has observed via Path MTU Discovery (PMTUD).

Note: The TCP maximum segment size is announced through TCP options in the original syn and the reply syn+ack packets. TCP maximum segment size is not negotiated, the RFC specifies that it is possible to have different TCP maximum segment size in each direction of the flow. Therefore, make sure you mangle both the TCP options of the original syn and the reply syn+ack packets.

Note for iptables users: 'tcp option maxseg size set rt mtu' is equivalent to '-j TCPMSS --clamp-mss-to-pmtu'.

You can also manually set to fixed value, eg. PPPoE takes 8 bytes to encapsulate packets, therefore, assuming MTU of 1500 bytes, 1500 - 20 (IPv4 Header) - 20 (TCP header) - 8 (PPPoE header) = 1452 bytes:

```bash
nft add rule ip filter forward tcp flags syn tcp option maxseg size set 1452
```

Other supported TCP options are: window, sack-permitted, sack, timestamp and eol.

## Interactions with conntrack

Keep in mind the interactions with conntrack, flows with mangled traffic must be untracked. You can do this in a single rule:

```bash
% nft add rule ip6 raw prerouting ip6 daddr fd00::1 ip6 daddr set fd00::2 notrack
```

For more information about packet headers to mangle check manpage `nft(8)`, Matching packet headers and Quick reference-nftables in 10 minutes.
