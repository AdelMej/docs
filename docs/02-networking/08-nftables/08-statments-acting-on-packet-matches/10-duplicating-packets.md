# Duplicating packets

Since Linux kernel 4.3, you can duplicate packets to another IPv4 or IPv6 destination address. This feature is useful for copying selected traffic from a local system to a remote host for further inspection.

## Basic duplication
The following rule duplicates all traffic to the destination address 172.20.0.2:

```bash
% nft add rule mangle prerouting dup to 172.20.0.2
```

## Forcing a specific device
You can force the `dup` statement to use a given device to route traffic from:

```bash
% nft add rule mangle prerouting dup to 172.20.0.2 device eth1
```

The rule above specifies that the duplicated packets must leave the system via the `eth1` interface. For this to work, your system must have a route to reach 172.20.0.2 via `eth1`.

## Using maps with duplication
You can also combine the `dup` statement with maps. For example, you can send duplicated packets to different remote hosts depending on the source IPv4 address:

```bash
% nft add rule mangle prerouting dup to ip saddr map { 192.168.0.1 : 172.20.0.2, 192.168.0.1 : 172.20.0.3 }
```
