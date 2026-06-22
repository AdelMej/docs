# Rejecting traffic

Note: Full reject support is available since Linux kernel 3.18.

The following rule shows how to reject any traffic from the network:

```bash
% nft add rule filter input reject
```

If you don't specify any reason, an ICMP/ICMPv6 port unreachable packet is sent to the origin.

You can narrow down this through the ct selector, so this only rejects traffic coming to the local machine which was not originated from us.

```bash
% nft add rule filter input ct state new reject
```

You can also specify the reject reason. For example:

```bash
% nft add rule filter input reject with icmp type host-unreachable
```

For ICMP, you can use the following reject reasons:

* **net-unreachable**: Destination network unreachable
* **host-unreachable**: Destination host unreachable
* **prot-unreachable**: Destination protocol unreachable
* **port-unreachable**: Destination port unreachable (this is the default)
* **net-prohibited**: Network administratively prohibited
* **host-prohibited**: Host administratively prohibited
* **admin-prohibited**: Communication administratively prohibited

You can also reject IPv6 traffic indicating the reject reason, for example:

```bash
% nft add rule ip6 filter input reject with icmpv6 type no-route
```

For ICMPv6, you can use the following reasons:

* **no-route**: No route to destination.
* **admin-prohibited**: Communication with destination administratively prohibited
* **addr-unreachable**: Address unreachable
* **port-unreachable**: Port unreachable

From the inet family, you can use an abstraction, the so-called `icmpx`, to reject the IPv4 and IPv6 traffic using one single rule. For example:

```bash
% nft add rule inet filter input reject with icmpx type no-route
```

This rule rejects IPv4 traffic with the reason "net unreachable" and the IPv6 traffic with the reason "no route". The mapping is shown in the following table:

| ICMPX REASON | ICMPv6 | ICMPv4 |
| :--- | :--- | :--- |
| admin-prohibited | admin-prohibited | admin-prohibited |
| port-unreachable | port-unreachable | port-unreachable |
| no-route | no-route | net-unreachable |
| host-unreachable | addr-unreachable | host-unreachable |
