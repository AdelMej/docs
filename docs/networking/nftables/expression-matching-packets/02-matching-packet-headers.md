# Matching packet headers

The `nft` command line utility supports the following layer 4 protocols: AH, ESP, UDP, UDPlite, TCP, DCCP, SCTP and IPComp.

## Matching ethernet headers

You can match packets on ethernet source or destination address or on EtherType:

`ether {saddr | daddr} «ether_addr»`
`ether type «ether_type»`

If you want to match ethernet traffic whose destination address is ff:ff:ff:ff:ff:ff, you can type the following command:

```bash
% nft add rule filter input ether daddr ff:ff:ff:ff:ff:ff counter
```

You can also match packets on IEEE 802.1Q VLAN fields, if present:

* `vlan type «ether_type»` - always vlan for 802.1Q
* `vlan id «12-bit integer»` - match VID, the VLAN ID
* `vlan cfi «1-bit integer»` - match DEI, Drop Eligible Indicator (formerly CFI, Canonical Format Indicator)
* `vlan pcp «3-bit integer»` - match IEEE P802.1p PCP, Priority Code Point

Do not forget that the layer 2 header information is only available in the input path.

## Matching ARP headers

You can match ARP headers:

* `arp htype «16-bit integer HTYPE»` - match hardware link protocol type (1 for ethernet)
* `arp ptype «ether_type»` - match EtherType
* `arp hlen «8-bit integer HLEN»` - match hardware address length in octets (6 for ethernet)
* `arp plen «8-bit integer PLEN»` - match internetwork protocol address length in octets (4 for IPv4)
* `arp operation «arp_op»` - match ARP operation
* `arp {saddr | daddr } ether «ether_addr»` - saddr matches SHA, Sender Hardware Address; daddr matches THA, Target Hardware Address
* `arp {saddr | daddr } ip «ipv4_addr»` - saddr matches SPA, Sender Protocol Address; daddr matches TPA, Target Protocol Address

## Matching IPv4 headers

You can also match traffic based on the IPv4 source and destination, the following example shows how to account all traffic that comes from 192.168.1.100 and that is addressed to 192.168.1.1:

```bash
% nft add rule filter input ip saddr 192.168.1.100 ip daddr 192.168.1.1 counter
```

Note that, since the rule is attached to the input chain, your local machine needs to use the 192.168.1.1 address, otherwise you won't see any matching.

To filter on a layer 4 protocol like TCP, you can use the protocol keyword:

```bash
% nft add rule filter input protocol tcp counter
```

## Matching ICMP traffic

You can drop all ICMP echo requests (popularly known as pings) via:

```bash
% nft add rule filter input icmp type echo-request counter drop
```

You can use `nft describe` to find nft's available icmp type keywords:

```bash
% nft describe icmp type
```

payload expression, datatype icmp_type (ICMP type) (basetype integer), 8 bits

pre-defined symbolic constants (in decimal):

* echo-reply: 0
* destination-unreachable: 3
* source-quench: 4
* redirect: 5
* echo-request: 8
* router-advertisement: 9
* router-solicitation: 10
* time-exceeded: 11
* parameter-problem: 12
* timestamp-request: 13
* timestamp-reply: 14
* info-request: 15
* info-reply: 16
* address-mask-request: 17
* address-mask-reply: 18

You can also be more specific by matching a single icmp code:

```bash
% nft describe icmp code
```

payload expression, datatype icmp_code (icmp code) (basetype integer), 8 bits

pre-defined symbolic constants (in decimal):

* net-unreachable: 0
* host-unreachable: 1
* prot-unreachable: 2
* port-unreachable: 3
* net-prohibited: 9
* host-prohibited: 10
* admin-prohibited: 13
* frag-needed: 4

```bash
% nft add rule filter output icmp code frag-needed counter accept
```

## Matching IPv6 headers

If you want to account IPv6 traffic that is addressed to abcd::100, you can type the following command:

```bash
% nft add rule filter output ip6 daddr abcd::100 counter
```

Do not forget to create an `ip6` table and register the corresponding chains to run the examples.

To filter on a layer 4 protocol like TCP, you can match on the `nexthdr` field of the IPv6 header:

```bash
% nft add rule filter input ip6 nexthdr tcp counter
```

Caution when using `ip6 nexthdr`, the value only refers to the next header, i.e. `ip6 nexthdr tcp` will only match if the ipv6 packet does not contain any extension headers. Specifically, using `ip6 nexthdr` to match on IPv6 ICMP matching might break because quoting RFC2710:

> MLD message types are a subset of the set of ICMPv6 messages, and MLD messages are identified in IPv6 packets by a preceding Next Header value of 58. All MLD messages described in this document are sent with a link-local IPv6 Source Address, an IPv6 Hop Limit of 1, and an IPv6 Router Alert option \[RTR-ALERT\] in a Hop-by-Hop Options header.

a hop-by-hop extension header comes between the IPv6 header and the ICMPv6 header, hence, the `nexthdr` field shows 58 in that case.

You could use `meta l4proto` to match on the transport protocol (ie. TCP, UDP, ICMPv6,...), this is walking down the headers until the real transport protocol is found.

If you specifically want to match on the ICMPv6 type, then nftables creates an implicit `meta l4proto` dependency that is not shown, therefore, there is no need for being verbose in your notation, ie.

```bash
% nft add rule filter input icmpv6 type { nd-neighbor-solicit, nd-router-advert, nd-neighbor-advert } accept
```

works fine to accept all ICMPv6 traffic regardless any possible extension headers.

NOTE: the syntax mixing IPv6/IPv4 notation is not supported yet: '::ffff:192.168.1.0'

## Matching transport protocol

The following rule shows how to match any kind of TCP traffic:

```bash
% nft add rule filter output ip protocol tcp
```

If you are on the `inet` family, then use `meta l4proto`:

```bash
% nft add rule inet filter output meta l4proto tcp
```

This allows you to match on the transport protocol regardless the packet is either IPv4 or IPv6. For IPv6, `meta l4proto` also skips any extension header until the first transport protocol is found.

## Matching TCP/UDP/UDPlite traffic

The following examples show how to drop all tcp traffic for low TCP ports (1-1024):

```bash
% nft add rule filter input tcp dport 1-1024 counter drop
```

Note that this rule is using an interval (from 1 to 1024).

To match on TCP flags, you need to use a binary operation. For example, to count packets that are not SYN ones:

```bash
% nft add rule filter input tcp flags != syn counter
```

More complex filters can be used. For example, to count and log TCP packets with flags SYN and ACK set:

```bash
% nft -i
nft> add rule filter output tcp flags & (syn | ack) == syn | ack counter log
```

This example drops TCP SYN packets which a MSS lower than 500:

```bash
% nft add rule inet filter input tcp flags syn tcp option maxseg size 1-500 drop
```

## Matching UDP/TCP headers in the same rule

The following example uses an anonymous `l4proto` set and a `th` (transport header) expression to match both TCP and UDP packets directed to port 53 (DNS):

```bash
% nft add rule filter input meta l4proto { tcp, udp }  th dport 53  counter packets 0 bytes 0  accept  comment \"accept DNS\"
```

Note: Before nftables 0.9.2 and Linux kernel 5.3 the `th` expression is not available. In this case you can use a raw payload expression to do the same job:

```bash
% nft add rule filter input meta l4proto { tcp, udp }  @th,16,16 53  counter packets 0 bytes 0  accept  comment \"accept DNS\"
```
