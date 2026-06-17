# Setting packet connection tracking metainformation

You can set some bits of the packet conntrack metainformation, as well as match on it.

## notrack - Bypass connection tracking

You can use the `notrack` statement (added in Linux kernel 4.9, nftables 0.7) to explicitly skip connection tracking for matched packets. To be effective your `notrack` rule must come before conntrack is triggered. You can ensure this by attaching it to a base chain with `prerouting` hook and `priority < NF_IP_PRI_CONNTRACK (-200)`. Using raw priority (-300) is a good choice.

The following example skips incoming traffic to tcp ports 80 (http) and 443 (https):

```nftables
nft add table my_table
nft add chain my_table prerouting { type filter hook prerouting priority -300 \; }
nft add rule my_table prerouting tcp dport { 80, 443 } notrack
```

## ct helper set - Assign conntrack helper

You can assign each packet a conntrack helper.

Instantiate a helper, using a named object:

```nftables
table filter {
      ct helper sip-5060 {
             type "sip" protocol udp;
      }

      ct helper tftp-69 {
             type "tftp" protocol udp;
      }

      ct helper ftp-standard {
             type "ftp" protocol tcp;
      }

      chain c {
             type filter hook prerouting priority 0;
      }
}
```

Your chain priority must be > -200, because conntrack registers at this priority. Otherwise, packets will not find any conntrack information (which is required to attach the helper).

Then, from the rules:

```bash
nft add rule filter c ct state new tcp dport 21 ct helper set "ftp-standard"
nft add rule filter c ct state new udp dport 5060 ct helper set "sip-5060"
nft add rule filter c ct state new udp dport 69 ct helper set "tftp-69"
```

You can use a map to assign many helpers using a single rule:

```bash
nft add rule filter c ct state new ct helper set ip protocol . th dport map { \
                        udp . 69 : "tftp-69", \
                        udp . 5060 : "sip-5060", \
                        tcp . 21 : "ftp-standard" }
```

Which sets the helper based on the transport protocol number and the transport destination port. You need nftables >= 0.8 and the kernel >= 4.12 to use this feature.

In case of a previous version of nftables, you can enable automatic assignment with:

```bash
echo 1 > /proc/sys/net/netfilter/nf_conntrack_helper
```

Also, with the sysctl parameter:
`net.netfilter.nf_conntrack_helper = 1`

## ct timeout set - Set conntrack timeout policy

## ct expectation set - Create a conntrack expectation

## ct mark set - Set conntrack mark

Save packet nfmark in conntrack:

```bash
ct mark set meta mark
```

## ct label set - Set conntrack label

Conntrack labels are 128-bit bitfields.

## ct zone set - Set conntrack zone

When setting the conntrack zone, it is crucial to do so before the packet gets picked up by conntrack. The below demo ruleset uses `ct zone set` rules in chains with raw priority, which accomplishes this:

```nftables
table inet zone_demo {

    chain PRE {
        type filter hook prerouting priority raw;

        iif eth3 ct zone set 23
    }

    chain OUT {
        type filter hook output priority raw;

        oif eth3 ct zone set 23
    }
}
```

## ct secmark set - Set conntrack secmark from packet secmark

New in nftables 0.9.3, you can set a secmark on a conntrack entry from a packet secmark:

```bash
ct secmark set meta secmark
```

NOTE you cannot set the ct secmark to a constant value, you must set it from a matched packet as above.

## ct event set - Set conntrack event

Restrict events reported by ctnetlink:

```bash
ct event set new,related,destroy
```
