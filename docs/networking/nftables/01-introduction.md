# Introduction to nftables

## Overview

nftables is the modern packet classification framework for the Linux kernel, introduced in kernel version 3.13. It is designed to replace the legacy xtables infrastructure (which includes `ip`, `ip6`, `arp`, and `eb` tables). While the legacy system is still maintained for existing codebases, new development should utilize nftables.

## Key Features

* **Command Line Utility:** nftables provides a dedicated `nft` command-line tool with a compact syntax inspired by `tcpdump`.
* **Compatibility Layer:** A compatibility layer exists that allows users to run traditional `iptables` commands over the new nftables kernel framework.
* **Generic Set Infrastructure:** The framework includes a generic set infrastructure, allowing for the creation of maps and concatenations. This enables rulesets to be organized into multidimensional trees, significantly reducing the number of rules inspected for each packet.
* **Netlink API:** It provides a Netlink API for third-party applications, consistent with other Linux Networking and Netfilter subsystems.

## Comparison with iptables

Nftables was developed to address several limitations inherent in the iptables framework:

### Syntax and Configuration

* **Compact Syntax:** Unlike iptables, which uses a `getopt_long()` based parser (requiring double dashes like `--key`), nftables uses a more compact syntax.
* **Explicit Definition:** In iptables, multiple tables and base chains are pre-defined even if unused. In nftables, there are no pre-defined tables or chains; every object (chains, sets, maps, flowtables) must be explicitly defined.

### Rule Architecture

* **Expressions and Statements:** An nftables rule consists of zero or more expressions followed by one or more statements. Expressions test packet fields or metadata linearly from left to right. If all match, the statements are executed.
* **Multiple Actions:** A single nftables rule can perform multiple actions (statements), whereas iptables rules typically have a single target action. However, a "verdict" statement (such as accept or drop) will end the rule's execution.

### Performance and Management

* **Dynamic Updates:** Nftables represents rulesets internally as linked lists rather than monolithic blobs, making it easier to add or delete rules without affecting the rest of the ruleset.
* **Dual Stack Support:** The `inet` family allows for simplified administration of both IPv4 and IPv6 traffic within the same base chains, removing the need for scripts to duplicate rulesets.
* **Protocol Support:** Due to a virtual machine approach, new protocols can often be supported via userspace software updates rather than requiring a full kernel upgrade.
* **Optional Counters:** Unlike iptables, nftables does not have built-in counters per chain or rule; they must be enabled as needed.

## Adoption

Nftables is included in all major Linux distributions. It is being integrated into or is already supported by several prominent projects, including:

* Fail2ban
* Suricata
* Keepalived
* SaltStack
* CoreOS
