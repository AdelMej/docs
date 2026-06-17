# Meters

## Dynamic set/map and stateful expressions
If you specify the `dynamic` flag to your set/map declaration, you can add elements to your set/map from the packet path. You can also attach a ratelimit per byte/packet/connection, counter and quota to such elements.

Among other things, the combination of dynamic set/map and stateful expressions provide a lot more flexible replacement for the `hashlimit` and `connlimit` matches in iptables.

## Using meters
The following example shows how to ratelimit inbound TCP connections to port 22 per source IP address:

```nftables
table ip filter {
      set my_ssh_ratelimit {
             type ipv4_addr
             timeout 60s
             flags dynamic
      }

      chain input {
             type filter hook input priority 0; policy drop;

             ct state new tcp dport 22 update @my_ssh_ratelimit { ip saddr limit rate 3/minute } accept
      }
}
```

For each packet matching this rule, it adds an element to the set whose key is `ip saddr` and it attaches a ratelimiter to such element. If the element already exists, the ratelimiter is applied and the timeout is refreshed to 60 seconds. After 60 seconds of no use, the element expires and the ratelimiter is released.

You can also use concatenations to ratelimit the inbound connections per IP source address and TCP destination port:

```nftables
table ip filter {
      set my_ssh_ratelimit {
             type ipv4_addr . inet_service
             timeout 60s
             flags dynamic
      }

      chain input {
             type filter hook input priority 0; policy drop;

             ct state new update @my_ssh_ratelimit { ip saddr . tcp dport limit rate 3/minute } accept
      }
}
```

## Doing connlimit with nft
Since 4.18, `ct count` allows you to count the number of existing connections based on connection tracking table, you can use it to limit the maximum number of established connections.

```nftables
table ip filter {
        set my_connlimit {
                type ipv4_addr
                size 65535
                flags dynamic
        }
 
        chain output {
                type filter hook output priority filter; policy accept;
                ct state new add @my_connlimit { ip saddr ct count over 20 } counter drop
        }
}
```

For each packet matching this rule, it adds an element to the set whose key is `ip saddr` and it allows a maximum number of 20 established connections to such IP source address.

**NOTE:** This policy does not specifies a timeout to sets intentionally. The connection tracking table timers apply in this case. For the same reason, you cannot use the `update` set statement which allows to refresh the timeout of your set element. Therefore, the `ct count` statement can only be used with the `add` set statement. If you define a timeout to your set and use it with `ct count`, you will hit an "Operation is not supported" error.

## Doing iptables hashlimit with nft
Meters replace `iptables hashlimit` in nft. From iptables v1.6.2 onward, you can use the tool `iptables-translate` to see how to translate `hashlimit` rules.

Almost all `hashlimit` options are available in nft, starting with `--hashlimit-mode`, it is replaced by the selector in a meter. All modes are available except no mode, a meter demands a selector, an iptables rule without `hashlimit-mode` isn't supported in nft. A simple rule translation is:

```bash
$ iptables-translate -A INPUT -m tcp -p tcp --dport 80 -m hashlimit --hashlimit-above 200/sec --hashlimit-mode srcip,dstport --hashlimit-name http1 -j DROP
nft add rule ip filter INPUT tcp dport 80 meter http1 { tcp dport . ip saddr limit rate over 200/second } counter drop
```

Notice that a meter is named, like `hashlimit`, and using multiple `hashlimit-modes` is similar to using a concatenation of selectors. Also, `--hashlimit-above` is translated to `limit rate over`, to simulate `--hashlimit-upto` just omit or replace `over` with `until` in the rule.

The options `--hashlimit-burst` and `--hashlimit-htable-expire` are translated to `burst` and `timeout` in a meter:

```bash
$ iptables-translate -A INPUT -m tcp -p tcp --dport 80 -m hashlimit --hashlimit-above 200kb/s --hashlimit-burst 1mb --hashlimit-mode srcip,dstport --hashlimit-name http2 --hashlimit-htable-expire 3000 -j DROP
nft add rule ip filter INPUT tcp dport 80 meter http2 { tcp dport . ip saddr timeout 3s limit rate over 200 kbytes/second burst 1 mbytes} counter drop
```

This rule shows how `timeout` and `burst` are used in a meter, also notice that meters, similarly to `hashlimit`, accepts limiting rates by bytes frequency instead of packets.

Another `hashlimit` option is to limit the traffic rate on subnets, of IP source or destination addresses, using the options `--hashlimit-srcmask` and `--hashlimit-dstmask`. This feature is available in nft by attaching a subnet mask to a meter selector, attach to `ip saddr` for source address and to `ip daddr` for destination adress:

```bash
$ iptables-translate -A INPUT -m tcp -p tcp --dport 80 -m hashlimit --hashlimit-upto 200 --hashlimit-mode srcip --hashlimit-name http3 --hashlimit-srcmask 24 -j DROP
nft add rule ip filter INPUT tcp dport 80 meter http3 { ip saddr and 255.255.255.0 limit rate 200/second } counter drop
```

This rule will limit packets rate, grouping subnets determined by the first 24 bits of the IP source address, from the incoming packets on port 80.

The remaining options, `--hashlimit-htable-max`, `--hashlimit-htable-size` and `--hashlimit-htable-gcinterval` don't apply to meters.
