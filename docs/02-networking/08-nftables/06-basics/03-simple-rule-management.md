# Simple Rule Management

Rules take action on network packets (e.g. accepting or dropping them) based on whether they match specified criteria. Each rule consists of zero or more expressions followed by one or more statements. Each expression tests whether a packet matches a specific payload field or packet/flow metadata. Multiple expressions are linearly evaluated from left to right: if the first expression matches, then the next expression is evaluated and so on. If we reach the final expression, then the packet matches all of the expressions in the rule, and the rule's statements are executed. Each statement takes an action, such as setting the netfilter mark, counting the packet, logging the packet, or rendering a verdict such as accepting or dropping the packet or jumping to another chain. As with expressions, multiple statements are linearly evaluated from left to right: a single rule can take multiple actions by using multiple statements. Do note that a verdict statement by its nature ends the rule.

Following are some basic operations and commands for configuring rules:

## 1 Appending new rules
To add new rules, you have to specify the corresponding table and the chain that you want to use, eg.

```bash
nft add rule filter output ip daddr 8.8.8.8 counter
```

Where filter is the table and output is the chain. The example above adds a rule to match all packets seen by the output chain whose destination is 8.8.8.8, in case of matching it updates the rule counters. Note that counters are optional in nftables. For those familiar with iptables, the rule appending is equivalent to -A command in iptables.

## 2 Listing rules
You can list the rules that are contained by a table with the following command:

```bash
nft list table filter
```

**Output:**
```nftables
table ip filter {
        chain input {
                 type filter hook input priority 0;
        }

        chain output {
                 type filter hook output priority 0;
                 ip daddr 8.8.8.8 counter packets 0 bytes 0
                 tcp dport ssh counter packets 0 bytes 0
        }
}
```

You can also list rules by chain, for example:

```bash
nft list chain filter ouput
```

**Output:**
```nftables
table ip filter {
        chain output {
                 type filter hook output priority 0;
                 ip daddr 8.8.8.8 counter packets 0 bytes 0
                 tcp dport ssh counter packets 0 bytes 0
        }
}
```

There are plenty of output text modifiers than can be used when listing your rules, to for example, translate IP addresses to DNS names, TCP protocols, etc.

## 3 Testing your rule
Let's test this rule with a simple ping to 8.8.8.8:

```bash
ping -c 1 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_req=1 ttl=64 time=1.31 ms
```

Then, if we list the rule-set, we obtain:

```bash
nft -nn list table filter
```

**Output:**
```nftables
table ip filter {
        chain input {
                 type filter hook input priority 0;
        }

        chain output {
                 type filter hook output priority 0;
                 ip daddr 8.8.8.8 counter packets 1 bytes 84
                 tcp dport 22 counter packets 0 bytes 0
        }
}
```

Note that the counters have been updated.

## 4 Adding a rule at a given position
If you want to add a rule at a given position, you have to use the handle as reference:

```bash
nft -n -a list table filter
```

**Output:**
```nftables
table filter {
        chain output {
                 type filter hook output priority 0;
                 ip protocol tcp counter packets 82 bytes 9680 # handle 8
                 ip saddr 127.0.0.1 ip daddr 127.0.0.6 drop # handle 7
        }
}
```

If you want to add a rule after the rule with handler number 8, you have to type:

```bash
nft add rule filter output position 8 ip daddr 127.0.0.8 drop
```

Now, you can check the effect of that command by listing the rule-set:

```bash
nft -n -a list table filter
```

**Output:**
```nftables
table filter {
        chain output {
                 type filter hook output priority 0;
                 ip protocol tcp counter packets 190 bytes 21908 # handle 8
                 ip daddr 127.0.0.8 drop # handle 10
                 ip saddr 127.0.0.1 ip daddr 127.0.0.6 drop # handle 7
        }
}
```

If you want to insert a rule before the rule with handler number 8, you have to type:

```bash
nft insert rule filter output position 8 ip daddr 127.0.0.8 drop
```

## 5 Removing rules
You have to obtain the handle to delete a rule via the -a option. The handle is automagically assigned by the kernel and it uniquely identifies the rule.

```bash
nft -a list table filter
```

**Output:**
```nftables
table ip filter {
        chain input {
                 type filter hook input priority 0;
        }

        chain output {
                 type filter hook output priority 0;
                 ip daddr 192.168.1.1 counter packets 1 bytes 84 # handle 5
        }
}
```

You can delete the rule whose handle is 5 with the following command:

```bash
nft delete rule filter output handle 5
```

*Note: There are plans to support rule deletion by passing the rule content directly, but this is not yet implemented. You'll have to use the handle to delete rules until that feature is implemented.*

## 6 Removing all the rules in a chain
You can delete all the rules in a chain with the following command:

```bash
nft flush chain filter output
```

You can also delete all the rules in a table with the following command:

```bash
nft flush table filter
```

## 7 Prepending new rules
To prepend new rules through the insert command:

```bash
nft insert rule filter output ip daddr 192.168.1.1 counter
```

This prepends a rule that will update per-rule packet and bytes counters for traffic addressed to 192.168.1.1. The equivalent in iptables is:

```bash
iptables -I OUTPUT -t filter -d 192.168.1.1
```

*Note that iptables always provides per-rule counters.*

## 8 Replacing rules
You can replace any rule via the replace command by indicating the rule handle, which you have to find by first listing the ruleset with option -a:

```bash
nft -a list ruleset
```

**Output:**
```nftables
table ip filter {
        chain input {
                type filter hook input priority 0; policy accept;
                ip protocol tcp counter packets 0 bytes 0 # handle 2
        }
}
```

To replace the rule with handle 2, specify its handle number and the new rule that you want to replace it:

```bash
nft replace rule filter input handle 2 counter
```

Listing the ruleset after the above replacement:

```bash
nft list ruleset
```

**Output:**
```nftables
table ip filter {
        chain input {
                type filter hook input priority 0; policy accept;
                counter packets 0 bytes 0 
        }
}
```

You can see that the old rule that counted TCP packets has been replaced by the new rule that counts all packets.
