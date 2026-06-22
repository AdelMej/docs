# Nftables families

Nftables simplifies network filtering by abstracting different networking levels into families, all managed by the single `nft` tool. The primary families include:

- **ip**: Handles IPv4 traffic (equivalent to legacy `iptables`).
- **ip6**: Handles IPv6 traffic (equivalent to legacy `ip6tables`).
- **inet**: Supports both IPv4 and IPv6 simultaneously, simplifying dual stack management. Rules can target both protocols or specific ones using `meta l4proto`.
- **arp**: Processes Layer 2 ARP traffic before any Layer 3 handling (equivalent to `arptables`).
- **bridge**: Manages traffic traversing bridges or switches (equivalent to `ebtables`).
- **netdev**: Used for base chains attached to specific network interfaces. It captures all traffic regardless of L2 or L3 protocols. This is particularly useful for early DDoS mitigation via the ingress hook, which is twice as efficient as prerouting chains, and for high-speed load balancing including Direct Server Return (DSR).
