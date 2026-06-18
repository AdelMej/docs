# Setting packet metainformation

You can set some metainformation in a packet. Please note that you require a Linux kernel >= 3.14 to use these features.

## packet mark
The following example shows how to set the packet mark:

```bash
% nft add rule route output mark set 123
```

## packet mark and conntrack mark
You can save/restore conntrack mark like in iptables.
In this example, the nf_tables engine set the packet mark to 1. In the last rule, that mark is store in the conntrack entry associated with the flow:

```bash
% nft add rule filter forward meta mark set 1
% nft add rule filter forward ct mark set mark
```

In this example, the conntrack mark is stored in the packet.

```bash
% nft add rule filter forward meta mark set ct mark
```

## packet secmark
You can use secmark objects to set SECMARK labels on packets.
New in nftables 0.9.3, you can set the packet secmark from its associated conntrack secmark:

```bash
meta secmark set ct secmark
```

## packet priority
You can set the priority of a packet.
This example shows a similar operation to what "-j CLASSIFY" does in iptables:

```bash
% nft add table mangle
% nft add chain postrouting {type route hook output priority -150\; }
% nft add rule mangle postrouting tcp sport 80 meta priority set 1
```

Warning: There is a bug in the priority syntax that will be fixed in following versions of nftables.

## pkttype
You can set the packet type:

```bash
meta pkttype set {pkt_type}
```

## nftrace
Setting the nftrace debugging bit in a packet will report the journey through the nf_tables stack:

```bash
% nft add rule filter forward udp dport 53 meta nftrace set 1
```

## combination of options
Given the flexible design of nftables, remember you can perform several actions to a packet in one rule:

```bash
% nft add rule filter forward ip saddr 192.168.1.1 meta nftrace set 1 meta priority set 2 meta mark set 123
```
