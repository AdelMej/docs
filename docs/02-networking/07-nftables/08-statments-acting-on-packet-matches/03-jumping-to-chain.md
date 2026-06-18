# Jumping to chain

It's often beneficial to structure your ruleset using a tree of chains. To do so, you first need to create at least one regular chain via:

```bash
% nft add chain ip filter tcp-chain
```

The example above creates the `tcp-chain` which will be used to add rules to filter tcp traffic, eg.

```bash
% nft add rule ip filter input ip protocol tcp jump tcp-chain
```

We can just add a simple rule to that `tcp-chain` to count packets and bytes:

```bash
% nft add rule ip filter tcp-chain counter
```

The listing should show something like:

```text
% nft list table filter
table ip filter {
        chain input {
                 type filter hook input priority 0;
                 ip protocol tcp jump tcp-chain
        }

        chain tcp-chain {
                 counter packets 8 bytes 2020
        }
}
```

The counters should update by generating simple TCP traffic.
Note: You can only jump to regular chains.

## jump vs goto

Please note the difference between `jump` and `goto`.

If you use `jump` to get packet processed in another chain, packet will return to the chain of the calling rule after the end.
However, if you use `goto`, packets will be processed in another chain but they will not return to the chain of the calling rule. In this case, the default policy applied to the packet will be the default policy of the original base chain which started processing the packet.

### Example of jump

Packet is: SRC=1.1.1.1 DST=2.2.2.2 TCP SPORT 111 DPORT 222

```text
table ip filter {
        chain input {
                 type filter hook input priority 0; policy accept;
                 # this is the 1º matching rule
                 ip saddr 1.1.1.1 ip daddr 2.2.2.2 tcp sport 111 tcp dport 222 jump other-chain
                 # this is the 3º matching rule
                 ip saddr 1.1.1.1 ip daddr 2.2.2.2 tcp sport 111 tcp dport 222 accept
        }

        chain other-chain {
                 # this is the 2º matching rule
                 counter packets 8 bytes 2020
        }
}
```

### Example of goto

Packet is: SRC=1.1.1.1 DST=2.2.2.2 TCP SPORT 111 DPORT 222

```text
table ip filter {
        chain input {
                 type filter hook input priority 0; policy accept;
                 # this is the 1º matching rule
                 # default policy 'accept' will be applied after other-chain ends processing
                 ip saddr 1.1.1.1 ip daddr 2.2.2.2 tcp sport 111 tcp dport 222 goto other-chain
                 # this rule will never be reached by this packet!
                 ip saddr 1.1.1.1 ip daddr 2.2.2.2 tcp sport 111 tcp dport 222 accept
        }

        chain other-chain {
                 # this is the 2º matching rule
                 counter packets 8 bytes 2020
        }
}
```

Note that only jump and goto actions to regular chains are allowed.
