# Sets

nftables includes a built-in generic set infrastructure that allows the use of any supported selector to build sets. This infrastructure enables the representation of maps and verdict maps. The set elements are internally represented using performance data structures such as hashtables and red-black trees.

## Anonymous sets
Anonymous sets have the following characteristics:
- They are bound to a rule; if the rule is removed, the set is released.
- They have no specific name (the kernel internally allocates an identifier).
- They cannot be updated (elements cannot be added or deleted once bound to a rule).

Example of creating a simple set:
```bash
% nft add rule ip filter output tcp dport { 22, 23 } counter
```
This rule catches all traffic going to TCP ports 22 and 23. If a match occurs, the counters are updated.

## Named sets
You can create a named set using the `nft add set` command. For example:
```bash
% nft add set ip filter blackhole { type ipv4_addr; comment "drop all packets from these hosts" ; }
```
- Set names must be 16 characters or less.
- The optional comment attribute requires at least nftables 0.9.7 and kernel 5.10.
- The `type` keyword indicates the data type of elements to be stored.

Example of adding elements:
```bash
% nft add element ip filter blackhole { 192.168.3.4 }
% nft add element ip filter blackhole { 192.168.1.4, 192.168.1.5 }
```

Example of using named sets in rules:
```bash
% nft add rule ip filter input ip saddr @blackhole drop
% nft add rule ip filter output ip daddr != @blackhole accept
```
Named sets can be updated at any time.

## nftables.conf syntax
When working with `nftables.conf`, you can define sets in several ways and reference them using `$VARIABLE_NAME` notation:

```text
define SIMPLE_SET = { 192.168.1.1, 192.168.1.2 }

define CDN_EDGE = {
    192.168.1.1,
    192.168.1.2,
    192.168.1.3,
    10.0.0.0/8
}

define CDN_MONITORS = {
    192.168.1.10,
    192.168.1.20
}

define CDN = {
    $CDN_EDGE,
    $CDN_MONITORS
}

# Allow HTTP(S) from approved IP ranges only
tcp dport { http, https } ip saddr $CDN accept
udp dport { http, https } ip saddr $CDN accept
```

## Named sets specifications
The following specifications are available for sets:

**type or typeof** (Obligatory): Determines the data type of the set elements.
Supported types for `type`:
- `ipv4_addr`
- `ipv6_addr`
- `ether_addr`
- `inet_proto`
- `inet_service`
- `mark`
- `ifname`

The `typeof` keyword (available since 0.9.4) allows you to use a high-level expression, letting nftables resolve the base type automatically.

**timeout**: Determines how long an element stays in the set. The format is "v1dv2hv3mv4s".
Example:
```bash
% nft add table ip filter
% nft add set ip filter ports {type inet_service; timeout 3h45s;}
```

**flags**:
- `constant`: Set content may not change while bound.
- `interval`: Set contains intervals.
- `timeout`: Elements can be added with a timeout.
Multiple flags should be separated by commas:
```bash
% nft add set ip filter flags_set {type ipv4_addr; flags constant, interval;}
```

**gc-interval**: Garbage collection interval (only usable if `timeout` or `flags timeout` are active). Follows the same format as timeouts.

**elements**: Used to initialize the set with values.
Example:
```bash
% nft add set ip filter daddrs {type ipv4_addr; flags timeout; elements={192.168.1.1 timeout 10s, 192.168.1.2 timeout 30s}}
```

**size**: Limits the maximum number of elements in the set.
Example:
```bash
% nft add set ip filter saddrs {type ipv4_addr; size 2;}
```

**policy**: Determines set selection policy:
- `performance` (default)
- `memory`
- `counter` (available since 0.9.5): Enables a counter per element.
- `auto-merge`: Automatically merges adjacent/overlapping set elements (valid for interval sets only).

## Listing named sets
To list the content of a named set:
```bash
% nft list set ip filter myset
```

## Query for element membership in a set
To check if an element exists in a set from its key:
```bash
% nft get element ip filter myset { 1.1.1.1 }
```
