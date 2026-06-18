# Conntrack helpers

Some internet protocols use multiple ports that are negotiated between endpoints during the initial connection. Netfilter's connection tracking system uses protocol helpers that look inside these negotiation packets to determine which ports will be part of the connection. The ct helper tells conntrack to expect packets to these ports; when such packets arrive conntrack assigns them related status.

To enable a conntrack helper in your ruleset:

1. Add a `ct helper <my_ct_helper>` stateful object which specifies the in-kernel name of the ct helper to use.
2. Add a filter rule for the initial protocol negotiation connection, using a `ct helper set "<my_ct_helper>"` statement to specify which ct helper to use.
3. Add filter rules as necessary to allow initial, established and related packets through your firewall.

The following heavily-commented example shows how to enable a helper for ftp traffic to the usual tcp/21 port:

```bash
table inet stateful_ftp {

    # 1. ct helper stateful object
    #   "ftp-standard" is the name of this ct helper stateful object.
    #   "ftp" is the in-kernel name of the ct helper for ftp.
    ct helper ftp-standard {
        type "ftp" protocol tcp;
    }
    
    chain PRE {
        type filter hook prerouting priority filter;

        # 2. Rule for initial ftp connection (control channel), specifying ct helper stateful object to use.
        #    NOTE "ftp-standard" is the name of the ct helper stateful object.
        tcp dport 21 ct helper set "ftp-standard"
    }
    
    # Example (partial) input filter base chain.
    #   NOTE default policy drop - we have to explicitly accept all allowed packets.
    chain IN {
        type filter hook input priority filter; policy drop;

        # 3a. Rule for ftp control channel.
        #     NOTE conntrack works here without needing helper.
        tcp dport 21 ct state new,established accept

        # 3b. Rule for related packets on ftp data channel.
        #     NOTE in-kernel ct helper name "ftp" is used here;
        #          trying to use ct helper stateful object name "ftp-standard" will NOT work.
        ct helper "ftp" accept        
    }
}
```

### Further comments on above example

* Rule 2 enables our `ftp-standard` ct helper in a prerouting chain. This makes the ct helper available to both forwarded and input traffic flows. If for some reason you want the ct helper to only be available for forwarded OR input flows, you can put Rule 2 in a forward or input chain, respectively.
* You may want to be more restrictive in Rule 3b. For example if you are using ftp passive mode you could use `ct state related ip daddr $ftphost ct helper "ftp" tcp dport { 1024-65535 } accept` and drop other tcp-related traffic.
* If you use a helper to forward traffic to a host behind your stateful router, it is critical that you use a `daddr` expression to restrict such traffic to that specific host in both Rule 2 (helper assignment) and in a rule similar to Rule 3b (related packets) in your forward filter chain(s). Otherwise the helper may allow arbitrary port forwarding, much like allowing untrusted remote hosts to inject "dnat"-based port forwarding rules.

### Supported conntrack helpers

Conntrack provides the following helpers:

* FTP
* TFTP
* NetBIOS
* IRC
* SIP
* H.323
* SNMP
* PPTP
* SANE
* Amanda

The conntrackd daemon also provides support for userspace helpers, such as:

* DHCPv6
* MDNS
* SLP
* SSDP
* RPC
* NFS version 3
* Oracle TNS
