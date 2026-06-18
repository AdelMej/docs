# Ruleset debug/tracing

Since nftables v0.6 and Linux kernel 4.6, ruleset debug/tracing is supported. This is an equivalent of the old iptables method `-J TRACE`, but with some great improvements.

The steps to enable debug/tracing are the following:

1. Give support in your ruleset (set `nftrace` in any of your rules)
2. Monitor the trace events from the nft tool

## 1 Enabling nftrace

To enable nftrace in a packet, use a rule with this statement:

```bash
meta nftrace set 1
```

After all, nftrace is part of the metainformation of a packet. Of course, you may only enable nftrace for a given matching packet. In the example below, we only enable nftrace for tcp packets:

```bash
ip protocol tcp meta nftrace set 1
```

Adjusting nftrace to only your subset of desired packets is key to properly debug the ruleset, otherwise you may get a lot of debug/tracing information which may be overwhelming.

## 2 Use a chain to enable tracing

The recommended way to enable tracing is to add a chain for this purpose. Register a `trace_chain` to enable tracing. If you already have a prerouting chain, then make sure your `trace_chain` priority comes before your existing prerouting chain.

```bash
nft add chain filter trace_chain { type filter hook prerouting priority -301; }
nft add rule filter trace_chain meta nftrace set 1
```

This example assumes you have an existing raw prerouting chain (at priority -300), hence, this is registering a trace chain right before this chain (at priority -301). Once you are done with rule tracing, you can just delete this chain to disable it:

```bash
nft delete chain filter trace_chain
```

## 3 monitoring tracing events

In nftables, getting the debug/tracing events is a bit different from the iptables world. Now, we have an event-based monitor for the kernel to notify the nft tool. The basic syntax is:

```bash
nft monitor trace
```

Each trace event is assigned an 'id' for you to easily follow different packets in the same trace session.

## 4 Complete example

Here is complete example of this debug/tracing mechanism in work. Assuming you have this ruleset:

```nftables
table ip filter {
        chain input {
                type filter hook input priority filter; policy drop;
                ct state established,related counter packets 2 bytes 292 accept
                ct state new tcp dport 22 counter packets 0 bytes 0 accept
        }
}
```

Load this ruleset:

```bash
nft -f ruleset.nft
```

Then, add a chain that enables tracing:

```bash
nft add chain ip filter trace_chain { type filter hook prerouting priority -1; }
```

This registers the `trace_chain` before the existing input chain. And the rule to enable the tracing:

```bash
nft add rule ip filter trace_chain meta nftrace set 1
```

Simple tracing test, by pinging one host:

```bash
ping -c 1 8.8.8.8
```

You run on a different terminal:

```bash
nft monitor trace
```

**Output:**

```text
trace id a95ea7ef ip filter trace_chain packet: iif "enp0s25" ether saddr 00:0d:b9:4a:49:3d ether daddr 3c:97:0e:39:aa:20 ip saddr 8.8.8.8 ip daddr 192.168.2.118 ip dscp cs0 ip ecn not-ect ip ttl 115 ip id 0 ip length 84 icmp type echo-reply icmp code net-unreachable icmp id 9253 icmp sequence 1 @th,64,96 24106705117628271805883024640 
trace id a95ea7ef ip filter trace_chain rule meta nftrace set 1 (verdict continue)
trace id a95ea7ef ip filter trace_chain verdict continue 
trace id a95ea7ef ip filter trace_chain policy accept 
trace id a95ea7ef ip filter input packet: iif "enp0s25" ether saddr 00:0d:b9:4a:49:3d ether daddr 3c:97:0e:39:aa:20 ip saddr 8.8.8.8 ip daddr 192.168.2.118 ip dscp cs0 ip ecn not-ect ip ttl 115 ip id 0 ip length 84 icmp type echo-reply icmp code net-unreachable icmp id 9253 icmp sequence 1 @th,64,96 24106705117628271805883024640 
trace id a95ea7ef ip filter input rule ct state established,related counter packets 168 bytes 53513 accept (verdict accept)
```

The trace id uniquely identifies a packet. The trace describes the packet entering the chain initially.

```text
trace id a95ea7ef ip filter trace_chain packet: iif "enp0s25" ether saddr 00:0d:b9:4a:49:3d ether daddr 3c:97:0e:39:aa:20 ip saddr 8.8.8.8 ip daddr 192.168.2.118 ip dscp cs0 ip ecn not-ect ip ttl 115 ip id 0 ip length 84 icmp type echo-reply icmp code net-unreachable icmp id 9253 icmp sequence 1 @th,64,96 24106705117628271805883024640
```

Then, the packet travel through the ruleset.

## 5 See also

Some external tools can automate all the steps described here:

* <https://github.com/aborrero/nftables-tracer> (python based)
* <https://github.com/aojea/nftrace> (golang based)
