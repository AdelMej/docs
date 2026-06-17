# Rate limiting matchings

You can ratelimit traffic through `limit`, which can be applied either per-packet or per-byte.

## Per packet

The following example shows how to accept a maximum of 10 ICMP echo-request packets per second:

```bash
% nft add rule filter input icmp type echo-request limit rate 10/second accept
```

This rule matches for packets below the 10/second rate. Those packets will be accepted, therefore you will need a rule to drop packets over the ratelimit — which will not match the rule above.

You can also express things the other way around:

```bash
% nft add rule filter input icmp type echo-request limit rate over 10/second drop
```

In the example above, `over` specifies that the rule is matching packets over the rate limit, and those packets will be dropped.

## Per byte

Since Linux kernel 4.3, you can also ratelimit per bytes:

```bash
% nft add rule filter input limit rate 10 mbytes/second accept
```

The rule above accepts traffic below the 10 mbytes/seconds rate. You can also use the `over` option to match packets going over the rate limit, e.g.:

```bash
% nft add rule filter input limit rate over 10 mbytes/second drop
```

The rule above drops packets over the 10 MBytes per second rate.

## Burst

You can also use the `burst` parameter to indicate the number of packets/bytes you can exceed the ratelimit:

```bash
% nft add rule filter input limit rate 10 mbytes/second burst 9000 kbytes accept
```

This indicates that you can exceed the ratelimit in 9000 kbytes. You can also use it for packets:

```bash
% nft add rule filter input icmp type echo-request limit rate 10/second burst 2 packets counter accept
```

So you can exceed the rate in 2 packets.

You can also use the `limit` expression for traffic policing in a rule using the ingress hook in the new netdev family (instead of using the tc command). The `over` keyword allows you to use `limit` intuitively in a chain with policy accept:

```bash
% nft add rule netdev filter ingress pkttype broadcast limit rate over 10/second drop
```
