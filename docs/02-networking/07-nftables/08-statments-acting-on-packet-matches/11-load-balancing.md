# Load balancing

## Load balancing

Since nftables v0.7, there is support in place to perform NAT load balancing. Don't forget the special NAT chain semantics: Only the first packet evaluates the rule, follow up packets rely on conntrack to apply the NAT information.

### 1 Round Robin

This method uses the nftables number generator. The example below is distributing new connections in a round-robin fashion between 192.168.10.100 and 192.168.20.200.

```bash
nft add rule nat prerouting dnat to numgen inc mod 2 map { \
               0 : 192.168.10.100, \
               1 : 192.168.20.200 }
```

You can also emulate flow distribution with different backend weights using intervals:

```bash
nft add rule nat prerouting dnat to numgen inc mod 10 map { \
               0-5 : 192.168.10.100, \
               6-9 : 192.168.20.200 }
```

The distribution can be based on ports as well:

```bash
nft add rule nat prerouting ip protocol tcp dnat to 192.168.1.100 : numgen inc mod 2 map { \
               0 : 4040 , \
               1 : 4050 }
```

Support for random and probability-based distributions also exists:

```bash
nft add rule nat prerouting numgen random mod 2 vmap { 0 : jump mychain1, 1 : jump mychain2 }
nft add rule nat prerouting numgen random mod 100 vmap { 0-49 : jump mychain1, 50-99 : jump mychain2 }
```

### 2 Consistent Hash-based Distribution

Using the nftables internal hashing mechanisms.

```bash
nft add rule x y dnat to jhash ip saddr . tcp dport mod 2 map { \
                0 : 192.168.20.100, \
                1 : 192.168.30.100 }
```

This relies on the Jenkins hash.

### 3 Using stateless NAT

You can perform load balancing through stateless NAT approach as well. You can combine this either with the round robin and consistent hash-based distribution approaches. The example below uses Round Robin flow distribution:

```bash
nft add rule t c tcp dport 80 ip daddr set numgen inc mod 2 map { 0 : 192.168.1.100, 1 : 192.168.1.101 }
```

This is more lightweight that stateful NAT given there is no flow tracking in place. This is indeed mangling packet headers.

### 4 Using Direct Server Return (DSR)

This example performs a DSR topology for connectionless flows from ingress:

```bash
nft add rule netdev t c udp dport 53 ether saddr set aa:bb:cc:dd:ff:ee ether daddr set numgen inc mod 2 map { 0 : xx:xx:xx:xx:xx:xx, 1: yy:yy:yy:yy:yy:yy } fwd to eth0
```

An approach for connection oriented flows can be performed as shown below:

```bash
nft add rule netdev t c tcp dport 80 ether saddr set aa:bb:cc:dd:ff:ee ether daddr set jhash ip saddr . tcp sport mod 2 map { 0 : xx:xx:xx:xx:xx:xx, 1: yy:yy:yy:yy:yy:yy } fwd to eth0
```

Note that xx:xx:xx:xx:xx:xx and yy:yy:yy:yy:yy:yy need to be replaced by the real destination MAC address. This is mangling packet headers as well.
