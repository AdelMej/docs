# Matching connection tracking stateful metainformation

nftables conntrack (ct) expressions enable stateful firewalls by matching packets that correspond to connections tracked by netfilter's Connection Tracking System.

## Conntrack metadata available

It is useful to refer to the conntrack data types.

### Conntrack-assigned metadata

Conntrack itself maintains most of its metadata for each tracked connection. The conntrack command-line tool makes it easy to list these metadata as well as manage the connections. Following is a sample partial output, run on a host serving an active sshd session. The `id` option includes the unique conntrack id in the output; the extended option produces the listing in `/proc/net/nf_conntrack` format.

```bash
% conntrack -L -o id,extended
...
ipv4     2 tcp      6 421957 ESTABLISHED src=192.168.0.2 dst=192.168.0.8 sport=34621 dport=22 src=192.168.0.8 dst=192.168.0.2 sport=22 dport=34621 [ASSURED] mark=6 use=1 id=2014938051
...
```

The following table lists each conntrack metadata field in the above output along with the nftables ct selector to match it.

### conntrack command output

| Column # | Description | Example value | ct Match | Notes |
| :--- | :--- | :--- | :--- | :--- |
| 1 | L3 protocol name | ipv4 | ct l3proto | |
| 2 | L3 protocol number | 2 | ct l3proto | As shown in `in.h` protocol value 6 indicates TCP. |
| 3 | L4 protocol name | tcp | ct protocol | |
| 4 | L4 protocol number | 6 | ct protocol | |
| 5 | timeout, s | 421957 | ct expiration | Seconds until conntrack entry is invalidated; reset to initial value when connection sees a new packet. Default TCP connection timeout is 5 days. You can change this via the conntrack sysfs setting `nf_conntrack_tcp_timeout_established`. |
| 6 | conntrack state | ESTABLISHED | ct state | |
| 7 | L3 source address, original direction | src=192.168.0.2 | ct original saddr | |
| 8 | L3 destination address, original direction | dst=192.168.0.8 | ct original daddr | |
| 9 | L4 protocol source, original direction | sport=34621 | ct original proto‑src | |
| 10 | L4 protocol destination, original direction | dport=22 | ct original proto‑dst | |
| 11 | L3 source address, reply direction | src=192.168.0.8 | ct reply saddr | |
| 12 | L3 destination address, reply direction | dst=192.168.0.2 | ct reply daddr | |
| 13 | L4 protocol source, reply direction | sport=22 | ct reply proto‑src | |
| 14 | L4 protocol destination, reply direction | dport=34621 | ct reply proto‑dst | |
| 15 | conntrack status | [ASSURED] | ct status | |
| 16 | conntrack mark | mark=6 | ct mark | Can also be set. |
| 17 | reference count | use=1 | | Mainly used by the garbage collector. |
| 18 | conntrack id | id=2014938051 | ct id | Unique id assigned to this conntrack entry. |

### User-assigned metadata

Conntrack mark, label and zone are user-settable, and nftables can subsequently match packets against them. In addition, `notrack`, ct helper set and ct event set affect conntrack and nftables operation.

## Matching conntrack metadata

### ct state - conntrack state

The `ct state` expression is almost certainly the one you will use the most. The conntrack state may be one of:

| State | Description |
| :--- | :--- |
| new | Netfilter has so far seen packets between this pair of hosts in only one direction. At least one of these packets is part of a valid initialization sequence, e.g. SYN packet for a TCP connection. |
| established | Netfilter has seen valid packets travel in both directions between this pair of hosts. For TCP connections, the three-way-handshake has been successfully completed. |
| related | This connection was initiated after the main connection, as expected from normal operation of the main connection. A common example is an FTP data channel established at the behest of an FTP control channel. |
| invalid | Assigned to packets that do not follow the expected behavior of a connection. |
| untracked | Dummy state assigned to packets that have been explicitly excluded from conntrack. See `notrack`. |

The following example ruleset shows how to deploy an extremely simple stateful firewall with nftables:

```nftables
table inet stateful_fw_demo {
    chain IN {
        type filter hook input priority filter; policy drop;

        ct state established,related accept
    }
}
```

The rule in the `IN` chain accepts packets that are part of an established connection, and related packets. Note the use of a comma-separated list of the conntrack states that you want to match. The default chain policy drops all other incoming packets. Thus, any attempt from a computer in the network to initiate a new connection to your computer will be blocked. However, traffic that is part of a flow that you have started will be accepted.

### ct helper - conntrack helper

The following example shows how to match packets based on the conntrack helper:

```bash
% nft add rule filter input ct helper "ftp" counter
```

### ct status - conntrack status

The conntrack status is a bitfield defined by `enum ip_conntrack_status` in `/include/uapi/linux/netfilter/nf_conntrack_common.h`. Nftables includes symbolic named constants for the conntrack status bits that make sense for filtering:

| Bit # | Kernel symbol | Nftables selector | Description | Notes |
| :--- | :--- | :--- | :--- | :--- |
| 0 | IPS_EXPECTED | expected | Set for expected connection. | Never changes. |
| 1 | IPS_SEEN_REPLY | seen-reply | Set upon seeing packets in both original and reply directions. | Once set, never unset. |
| 2 | IPS_ASSURED | assured | Set when this conntrack entry should never be early-expired. | |
| 3 | IPS_CONFIRMED | confirmed | Set when originating packet has left the local host. | |
| 4 | IPS_SRC_NAT | snat | Set when connection needs SNAT in original dirction. | Never changed. |
| 5 | IPS_DST_NAT | dnat | Set when connection needs DNAT in original dirction. | Never changed. |
| 6 | IPS_SEQ_ADJUST | | Set when connection needs TCP sequence adjustment. | |
| 7 | IPS_SRC_NAT_DONE | | | |
| 8 | IPS_DST_NAT_DONE | | | |
| 9 | IPS_DYING | dying | Set when connection is dying (removed from lists). | Cannot be unset. |
| 10 | IPS_FIXED_TIMEOUT | | Set when connection has fixed timeout. | |
| 11 | IPS_TEMPLATE | | Set when conntrack entry is a template. | |
| 12 | IPS_UNTRACKED | | Obsolete, not used anymore. | Formerly: set when conntrack entry is untracked (fake). |
| 13 | IPS_HELPER | | Set when conntrack entry got a helper explicitly attached. | |
| 14 | IPS_OFFLOAD | | Set when conntrack entry has been offloaded to a flow table. | |
| 15 | IPS_HW_OFFLOAD | | Set when conntrack entry has been offloaded to hardware. | |

Match single flag: `ct status snat`
Match multiple flags: `ct status {expected,dnat}`
Match inverted flag: `(ct status & dnat) != dnat` (See cannot use `!=` with `ct status`.)

### ct mark - conntrack mark

The following example shows how to match packets based on the conntrack mark:

```bash
% nft add rule filter input ct mark 123 counter
```

To know more about conntrack marks and packet marks, see Setting packet metainformation.

### ct label

If a conntrack label has been applied to a packet, you can match such packets with this expression.

### ct zone

Similar to `ct label`, if a conntrack zone has been assigned to a packet, you can then match such packets using this expression. You can optionally include a packet direction with this match: `ct [original | reply] zone zone`.

### ct direction

A packet with original direction is traveling in the same direction as the first packet seen for this connection, e.g. in the direction of the original SYN packet for a TCP connection. A packet with reply direction is traveling in the direction opposite to original.

### ct expiration

You can match packets based on when their associated connection is set to expire, e.g. `ct expiration < 1m30s`.

### ct count

You can match based on the current number of tracked connections matching the specified properties. See `connlimits`.

### ct packets

Match based on how many packets have previously traversed the connection associated with a packet. If you specify an optional direction, only packets in that direction are counted. Otherwise, packets in both directions are counted. For example:
`ct [original | reply] packets < 10000`

### ct bytes

Match based on how many total bytes have previously traversed the connection associated with a packet. If you specify an optional direction, only traffic in that direction is counted. Otherwise, traffic in both directions is counted. For example:
`ct [original | reply] bytes < 100000`

### ct avgpkt

Match based on the average packet size (bytes/packet) seen so far on the connection associated with a packet. If you specify an optional direction, only traffic in that direction is counted. Otherwise, traffic in both directions is counted. For example:
`ct [original | reply] avgpkt < 100`

### ct l3proto - conntrack L3 protocol

Matches the L3 protocol of the conntrack entry associated with a packet. You can optionally include a packet direction with this match:
`ct [original | reply] l3proto l3_protocol.`

### Conntrack L3 source or destination address

This match requires specifying both direction and L3 protocol (ip or ip6 currently recognized):
`ct {original | reply} {ip | ip6} {saddr | daddr}`

### ct protocol- conntrack L4 protocol

Matches the L4 protocol of the conntrack entry associated with a packet. You can optionally include a packet direction with this match:
`ct [original | reply] protocol l4_protocol.`

### Conntrack L4 protocol source or destination

This match requires specifying the direction:
`ct {original | reply} {proto-src | proto-dst}`

### ct id

This expression will match packets associated with a specific connection with given conntrack id (as from `conntrack -L -o id`). You can optionally include a packet direction with this match: `ct [original | reply] id ct_id`.
