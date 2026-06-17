# Troubleshooting

In this section, you can find frequently asked questions that have been posted on the Netfilter mailing list.

## Question 1: Address family not supported by protocol problems

If I try to start nft, I get this error:

```
% nft list table filter
<cmdline>:1:1-17: Error: Could not receive sets from kernel: Address family not supported by protocol
list table filter
^^^^^^^^^^^^^^^^^
```

**Answer:** You have to create the table before you can actually list it, i.e., `nft add table filter`. Please see how to configure tables. Moreover, make sure you also compiled family support, e.g., `CONFIG_NF_TABLES_IPV4` and that the module can be loaded (e.g., `nf_tables_ipv4`).

## Question 2: No such file or directory when adding chain

```
nft> add chain arp filter input {type nat hook input priority 0 ;}
<cli>:1:1-64: Error: Could not add chain: No such file or directory
add chain arp filter input {type nat hook input priority 0 ;}
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

This means that the chain type for the specified family does not exist. In the example above, the problem is that the NAT chain type does not exist for the ARP family. You may also hit this problem if you forgot to compile the module that enables this chain type in your Linux kernel.

## Question 3: Operation not supported when adding chain

For example:

```
nft> add chain ip filter forward {type nat hook forward priority 0 ;}
<cli>:1:1-64: Error: Could not add chain: Operation not supported
add chain filter forward {type nat hook forward priority 0 ;}
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

This means that the chain type for the specified family is not available in that hook. In the above example, the problem is that the available hooks for the NAT chain type are: prerouting, input, output and postrouting.

## Question 4. How do nftables and iptables interact when used on the same system?

What happens when you mix Iptables and Nftables? How do they interact?

| Rule | nft | iptables |
| :--- | :--- | :--- |
| 1 | Empty | Empty |
| 2 | Accept | Block |
| 3 | Accept | Accept |
| 4 | Block | Accept |
| 5 | Blank | Accept |

**Results:** Pass, Pass, Unreachable, Unreachable, Pass.

## Question 5: Could not process rule: Operation not permitted

First step is to use `sudo` or a root shell to run the `nft` command.

```
sudo nft list ruleset
```

Access from userland to kernel (via libnftnl API) requires root privilege.

## Question 6: Chain of type "filter" is not supported, perhaps kernel support is missing

Most likely used a rule statement that does not belong in that particular family. Usually means one of the following statements got illegally used inside a specific family chain or outside a specific chain hook/type:

| Rule statement | Restriction |
| :--- | :--- |
| devices | only netdev family, filter type & ingress hook |
| dnat to | only ip/ip6/inet family, nat type & prerouting/input hook |
| flags offload | only filter type, ingress hook & netdev family |
| fwd to | only ip/ip6 family, filter type & forward hook |
| ibriport | only bridge family, filter type & input/forward/output hook |
| ibrname | only bridge family, filter type & forward hook |
| meta ibriport | only bridge family, filter type & input/forward hook |
| meta obriport | only bridge family, filter type & output/forward hook |
| obriport | only bridge family, filter type & forward/output hook |
| obrname | only bridge family, filter type & output/forward hook |
| redirect | only ip/ip6/inet family, nat chain type & prerouting/input hook |
| snat to | only ip/ip6/inet family, nat type & postrouting/output hook |

### Invalid rule statements in specific families

**In ip (or implicit) family chains:**

* `devices` (netdev family only)
* `dnat` to (only in nat type & prerouting/input hook)
* `flags` offload (netdev family only)
* `fwd` to (only in filter type & forward hook)
* `ibriport` (bridge family only)
* `ibrname` (bridge family only)
* `meta` ibriport (bridge family only)
* `meta` obriport (bridge family only)
* `obriport` (bridge family only)
* `obrname` (bridge family only)
* `redirect` (only in nat type & prerouting/input hook)

**In ip6 family chains:**

* `devices` (netdev family only)
* `dnat` to (only in nat type & prerouting/input hook)
* `flags` offload (netdev family only)
* `fwd` to (only in filter type & forward hook)
* `ibriport` (bridge family only)
* `ibrname` (bridge family only)
* `meta` ibriport (bridge family only)
* `meta` obriport (bridge family only)
* `obriport` (bridge family only)
* `obrname` (bridge family only)
* `redirect` (only in nat type & prerouting/input hook)
* `snat` to (only in nat type & postrouting/output hook)

**In inet family chains:**

* `devices` (netdev family only)
* `dnat` to (only in nat type & prerouting/input hook)
* `flags` offload (netdev family only)
* `fwd` to (ip/ip6 family only)
* `ibriport` (bridge family only)
* `ibrname` (bridge family only)
* `meta` ibriport (bridge family only)
* `meta` obriport (bridge family only)
* `obriport` (bridge family only)
* `obrname` (bridge family only)
* `redirect` (only in nat chain type & prerouting/input hook)
* `snat` to (only in nat type & postrouting/output hook)

**In netdev family chain:**

* `devices` (only in filter type & ingress hook)
* `dnat` to (ip/ip6/inet family only)
* `flags` offload (only in filter type & ingress hook)
* `fwd` to (ip/ip6 family only)
* `ibriport` (bridge family only)
* `ibrname` (bridge family only)
* `meta` ibriport (bridge family only)
* `meta` obriport (bridge family only)
* `obriport` (bridge family only)
* `obrname` (bridge family only)
* `redirect` (ip/ip6/inet family only)
* `snat` to (ip/ip6/inet family only)

**In bridge family chain:**

* `devices` (netdev family only)
* `dnat` to (ip/ip6/inet family only)
* `flags` offload (netdev family only)
* `fwd` to (ip/ip6 family only)
* `redirect` (ip/ip6/inet family only)
* `snat` to (ip/ip6/inet family only)

**In arp family chain:**

* `devices` (netdev family only)
* `dnat` to (ip/ip6/inet family only)
* `flags` offload (netdev family only)
* `fwd` to (ip/ip6 family only)
* `ibriport` (bridge family only)
* `ibrname` (bridge family only)
* `meta` ibriport (bridge family only)
* `meta` obriport (bridge family only)
* `obriport` (bridge family only)
* `obrname` (bridge family only)
* `redirect` (ip/ip6/inet family only)
* `snat` to (ip/ip6/inet family only)
