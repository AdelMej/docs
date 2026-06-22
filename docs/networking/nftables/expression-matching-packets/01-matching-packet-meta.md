# Matching packet metainformation

The meta selectors allow you to match — and in some cases, set — packet metainformation. That is, information the local host has about the packet (such as how / when it was received) that is not necessarily carried in the packet itself.

## Matching by packet info

The following meta selectors match packets by information carried by the packet itself:

| Keyword | Settable | Description | Data Type | Notes |
| :--- | :--- | :--- | :--- | :--- |
| pkttype | Y | packet type (unicast, broadcast, multicast, other) | pkt_type | |
| length | | packet length in bytes | integer (32 bit) | |
| protocol | | packet protocol / EtherType protocol value | ether_type | as in skb->protocol |
| nfproto | | netfilter packet protocol family | integer (32 bit) | like ipv4, ipv6, etc...; useful only in inet table |
| l4proto | | layer 4 protocol | integer (8 bit) | like tcp, udp, etc...; skips ipv6 extension headers |

## Matching by interface

The following meta selectors match packets based on incoming or outgoing interfaces:

| Keyword | Settable | Description | Data Type | Notes |
| :--- | :--- | :--- | :--- | :--- |
| iif | | input interface index | iface_index | Faster than iifname as it only has to compare a 32-bit unsigned integer instead of a string. The interface index is dynamically allocated, so don't use this for interfaces that are dynamically created and destroyed, eg. ppp0. |
| iifname | | input interface name | ifname | |
| iiftype | | input interface type | iface_type | |
| iifkind | | input interface kind name | ifkind | |
| iifgroup | | input interface group | devgroup | |
| oif | | output interface index | iface_index | Faster than oifname as it only has to compare a 32-bit unsigned integer instead of a string. The interface index is dynamically allocated, so don't use this for interfaces that are dynamically created and destroyed, eg. ppp0. |
| oifname | | output interface name | ifname | |
| oiftype | | output interface type | iface_type | |
| oifkind | | output interface kind name | ifkind | |
| oifgroup | | output interface group | devgroup | |
| ibrname | | input bridge interface name | ifname | equivalent to obsolete ibriport keyword |
| obrname | | output bridge interface name | ifname | equivalent to obsolete oibriport keyword |
| ibrvproto | | input bridge vlan protocol | ether_type | |
| ibrpvid | | input bridge port pvid | integer (16 bit) | |
| sdif | | slave device interface index | integer | |
| sdifname | | slave device interface name | ifname | |

An example rule that uses iifname to accept all traffic entering the loopback pseudodevice lo:
`% nft add rule filter input meta iifname lo accept`

## Matching by packet mark, routing class and realm

| Keyword | Settable | Description | Data Type | Notes |
| :--- | :--- | :--- | :--- | :--- |
| mark | Y | packet mark | mark | |
| priority | Y | tc packet priority | tc_handle | detailed usage example |
| rtclassid | | routing realm | realm | Routing realm references: linux-ip.net, policyrouting.org |

You can match packets whose mark is 123 with the following rule:
`nft add rule filter output meta mark 123 counter`

Since nftables v0.7 you can match the packet priority, the tc classid:
`% nft add rule filter forward meta priority abcd:1234`
Packet without set priority can be matched using `meta priority none`:
`% nft add rule filter forward meta priority none`
See also: nexthop and fib selectors

## Matching by socket UID / GID

| Keyword | Settable | Description | Data Type | Notes |
| :--- | :--- | :--- | :--- | :--- |
| skuid | | UID associated with originating socket | uid | |
| skgid | | GID associated with originating socket | gid | |

You can use your user name to match traffic, eg.
`% nft add rule filter output meta skuid pablo counter`
Or the 32-bits unsigned integer (UID) in case there is no entry in /etc/passwd for a given user.
`% nft add rule filter output meta skuid 1000 counter`

Let's just generate some HTTP traffic to test this rule:
`% wget --spider http://www.google.com`

Then, if you check the counters, you can verify that the packets are matching that rule.
`% nft list table filter`

```
table ip filter {
        chain output {
                 type filter hook output priority 0;
                 skuid pablo counter packets 7 bytes 510
        }

        chain input {
                 type filter hook input priority 0;
        }
}
```

**Important:** Beware if you test this with ping, it is usually installed with suid so that traffic will match the root user (uid=0).

## Matching by time

| Keyword | Settable | Description | Data Type | Notes |
| :--- | :--- | :--- | :--- | :--- |
| time | | timestamp of packet reception | time | Can specify as: integer: ns since epoch, or string: date in ISO format. |
| day | | day of week | day | Can specify as: integer: 0 = Sunday to 6 = Saturday, or case-insensitive string: "Monday", "tuesday", etc. Unique abbreviations also work: "fri", "Sat". |
| hour | | hour of day | hour | 24-hour "HH:MM:SS", with seconds optional. |

## Matching by security selectors

| Keyword | Settable | Description | Data Type | Notes |
| :--- | :--- | :--- | :--- | :--- |
| cpu | | CPU number processing the packet | integer (32 bit) | |
| cgroup | | socket control group ID | integer (32 bit) | |
| secmark | Y | packet secmark | integer (32 bit) | |
| ipsec | | true if packet was ipsec encrypted | boolean (1 bit) | equivalent to obsolete secpath keyword |

## Matching by miscellaneous selectors

In addition to those in the above subsections, the following miscellaneous meta selectors are available:

| Keyword | Settable | Description | Data Type | Notes |
| :--- | :--- | :--- | :--- | :--- |
| nftrace | Y | nftrace debugging bit | boolean (1 bit) | |
| random | | pseudo-random number | integer (32 bit) | |
