# Verdict Maps (vmaps)

Verdict maps, created using the `vmap` statement, allow you to map elements directly to verdict statements. Internally they use the generic set infrastructure and therefore share some semantics and options. Some documentation may refer to vmaps as dictionaries.

## Anonymous vmaps
The following example shows how to create a tree of chains whose traversal depends on the layer 4 protocol type:

```bash
% nft add rule ip filter input ip protocol vmap { tcp : jump tcp-chain, udp : jump udp-chain , icmp : jump icmp-chain }
```

This example above assumes that you've already created the `tcp-chain`, `udp-chain` and `icmp-chain` custom chains. Then, by attaching simple rules to account the traffic:

```bash
% nft add rule filter icmp-chain counter
% nft add rule filter tcp-chain counter
% nft add rule filter udp-chain counter
```

You can check that the classification is happening:

```nftables
table ip filter {
        chain input {
                 type filter hook input priority 0;
                 ip protocol vmap { udp : jump udp-chain, tcp : jump tcp-chain, icmp : jump icmp-chain}
        }

        chain tcp-chain {
                 counter packets 4 bytes 520
        }

        chain udp-chain {
                 counter packets 4 bytes 678
        }

        chain icmp-chain {
                 counter packets 4 bytes 336
        }
}
```

The branching chain structure illustrated above is a powerful way to reduce the number of linear list inspections needed to classify your packets.

## Named vmaps
You can also declare named vmaps, which can then be populated dynamically. For example:

```bash
% nft add map filter mydict { type ipv4_addr : verdict\; }
```

Then, you can add elements:

```bash
% nft add element filter mydict { 192.168.0.10 : drop, 192.168.0.11 : accept }
```

You can bind this vmap to a rule using the following command:

```bash
% nft add rule filter input ip saddr vmap @mydict
```

Note that in the above example we had to specify the correct `ipv4_addr` data type when creating the `mydict` vmap. In case you are unsure of the correct data type to specify, recall that you can ask nft:

```bash
% nft describe ip saddr
payload expression, datatype ipv4_addr (IPv4 address) (basetype integer), 32 bits
```

## Valid vmap Verdicts
The name verdict map can be taken literally: each element must map to a single simple verdict statement. To recap the `nft` man page section, the complete list of such verdict statements is:

* accept
* drop
* queue
* continue
* return
* jump chain
* goto chain
