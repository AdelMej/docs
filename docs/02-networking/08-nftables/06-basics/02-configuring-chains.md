# Configuring chains

In `nftables`, as in `iptables`, rules are attached to chains. However, unlike `iptables`, there are no predefined chains like INPUT or OUTPUT. To filter packets at a particular processing step, you must explicitly create a base chain with a name of your choosing and attach it to the appropriate Netfilter hook. This allows for flexible configurations without slowing down Netfilter with unnecessary built-in chains.

## 1 Syntactic conventions

To use `nftables` commands, users must be familiar with their grammar. Because shells treat certain characters as metacharacters, it is recommended to quote `nft(8)` commands.

Examples beginning with a hash character (`#`) are valid shell code. Examples without the hash character are either synopses of `nftables` grammar or complete rulesets.

Shell code generally follows this form:

```bash
# nft 'nftables commands go here'
```

Enclosing commands in single quotes prevents the shell from interpreting characters like semicolons. Alternatively, you can run `nft(8)` in interactive mode:

```bash
# nft -i
```

In interactive mode, `nft(8)` interprets all further input until interrupted, EOF (Ctrl+D), or the "quit" command.

Syntax examples use square brackets `[]` for optional components and angle brackets `<>` for user-specified values.

## 2 Adding base chains

Base chains are registered into Netfilter hooks and see packets flowing through the Linux TCP/IP stack. The syntax is:

```bash
add chain [<family>] <table_name> <chain_name> { type <type> hook <hook> [device <device>] priority <priority> ; [policy <policy> ;] [comment <comment> ;] }
```

Example of adding an input chain to the filter table:

```bash
# nft 'add chain ip filter input { type filter hook input priority 0; }'
```

This registers the input chain to the input hook to see packets addressed to local processes. The priority determines the ordering of chains at a specific hook. For example, priorities -12, -1, 0, and 10 would be consulted in that order.

Example for an output chain:

```bash
# nft 'add chain ip filter output { type filter hook output priority 0; }'
```

**Note:** If you omit the configuration inside the curly braces, you create a regular chain that does not see any packets (similar to `iptables -N`).

Since `nftables` 0.5, you can specify a default policy:

```bash
# nft 'add chain ip filter output { type filter hook output priority 0; policy accept; }'
```

The available policies are `accept` and `drop`.

When adding a chain on an ingress hook, the device is mandatory:

```bash
# nft 'add chain netdev filter eth0_filter { type filter hook ingress device eth0 priority 0; }'
```

### 2.1 Base chain types

* **filter**: Used to filter packets. Supported by `arp`, `bridge`, `ip`, `ip6`, and `inet` table families.
* **route**: Used to reroute packets if IP header fields or marks are modified. Equivalent to the `mangle` table's output hook semantics. Supported by `ip`, `ip6`, and `inet`.
* **nat**: Used for Network Address Translation (NAT). Only the first packet of a flow hits this chain; subsequent packets bypass it. Do not use this for filtering. Supported by `ip`, `ip6`, and `inet`.

### 2.2 Base chain hooks

* **ingress**: Sees packets immediately after they are passed up from the NIC driver (before prerouting).
* **prerouting**: Sees all incoming packets before any routing decision is made.
* **input**: Sees incoming packets addressed to and routed to the local system/processes.
* **forward**: Sees incoming packets not addressed to the local system.
* **output**: Sees packets originating from processes in the local machine.
* **postrouting**: Sees all packets after routing, just before they leave the local system.

### 2.3 Base chain priority

Priority defines the ordering among other base chains and Netfilter operations at the same hook. For example, a priority of -300 on the prerouting hook places it before connection tracking.

**Important Note:** An `accept` verdict is not necessarily final; if another chain exists with the same hook type but a later priority, the packet will traverse it. However, a `drop` verdict takes immediate effect, and no further rules or chains are evaluated.

### 2.4 Base chain policy

The default verdict applied to packets reaching the end of a chain:

* **accept**: The packet keeps traversing the network stack.
* **drop**: The packet is discarded if it reaches the end of the base chain.
If no policy is explicitly selected, `accept` is used by default.

## 3 Adding regular chains

Regular chains are analogous to user-defined chains in `iptables`. They are not attached to hooks and do not see traffic by themselves. They are processed when a base chain uses `jump` or `goto` actions.

```bash
add chain [family] <table_name> <chain_name> [comment <comment>]
```

## 4 Deleting chains

Chains must be empty before they can be deleted:

```bash
delete chain [family] <table_name> <chain_name>
```

If a chain is in use, the kernel will return an error. You must flush the ruleset in that chain first.

## 5 Flushing chains

To delete all rules within a chain:

```bash
flush chain [family] <table_name> <chain_name>
```

## 6 Example configuration: Filtering traffic for your standalone computer

Example of defining rules for an IPv4 standalone computer:

```bash
# nft 'add table ip filter'
# nft 'add chain ip filter input { type filter hook input priority 0; }'
# nft 'add chain ip filter output { type filter hook output priority 0; }'
```

Declarative style (equivalent to the commands above):

```nftables
table ip filter {
    chain input {
        type filter hook input priority filter; policy accept;
    }

    chain output {
        type filter hook output priority filter; policy accept;
    }
}
```
