# Intervals

## Intervals

Intervals are expressed as value-value.
The following rule drops incoming traffic addressed to the IP address interval 192.168.0.1 to 192.168.0.250:

```bash
nft add rule filter input ip daddr 192.168.0.1-192.168.0.250 drop
```

You can use intervals of any sort of constant value. This example uses a TCP port interval:

```bash
nft add rule filter input tcp ports 1-1024 drop
```

You can also use intervals from sets, the following example shows how to blacklist two intervals of IP addresses:

```bash
nft add rule ip filter input ip saddr { 192.168.1.1-192.168.1.200, 192.168.2.1-192.168.2.200 } drop
```

Intervals work the same way in verdict maps:

```bash
nft add rule ip filter forward ip daddr vmap { 192.168.1.1-192.168.1.200 : jump chain-dmz, 192.168.2.1-192.168.20.250 : jump chain-desktop }
```
