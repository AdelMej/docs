# Synproxy

A netfilter synproxy intercepts new TCP connections and handles the initial 3-way handshake using syncookies instead of conntrack to establish the connection. Running synproxy on a listening server port thus prevents a SYN flood attack on that port from consuming limited conntrack resources.

Using synproxy requires disabling the conntrack loose tracking option:

```bash
% echo 0 > /proc/sys/net/netfilter/nf_conntrack_tcp_loose
```

With this setting, the final handshake ACK from the remote host will be marked INVALID and won't create a new conntrack entry. In our accept rule for such connections, we explicitly accept these "INVALID" packets and pass them along to our listening service (see examples below).

Also, because synproxy relies on syncookies and tcp timestamps, ensure these are enabled:

```bash
% echo 1 > /proc/sys/net/ipv4/tcp_syncookies
% echo 1 > /proc/sys/net/ipv4/tcp_timestamps
```

You may also want to increase conntrack resources:

`/sys/module/nf_conntrack/parameters/hashsize`
`/proc/sys/net/netfilter/nf_conntrack_max`

## Anonymous synproxy

An anonymous synproxy exists in the context of the single rule to which it is attached. The following ruleset configures an anonymous synproxy for port tcp/8888:

```bash
table ip anon_synproxy_demo {

    chain PRE {
        type filter hook prerouting priority raw; policy accept;

        tcp dport 8888 tcp flags syn notrack
    }

    chain IN {
        type filter hook input priority filter; policy accept;

        tcp dport 8888 ct state invalid,untracked synproxy mss 1460 wscale 7 timestamp sack-perm
        ct state invalid drop
    }
}
```

## synproxy parameters

| Parameter | Description | Data Type | Notes |
| :--- | :--- | :--- | :--- |
| **mss** | maximum segment size | unsigned integer (16 bit) | Must match your backend server. |
| **wscale** | window scale | unsigned integer (8 bit) | Must match your backend server. |
| **timestamp** | pass client timestamp option to backend | symbol for 1-bit flag | Disabled if not present. Needed for window scaling and selective acknowledgement. |
| **sack-perm** | pass client selective acknowledgement option to backend | symbol for 1-bit flag | Disabled if not present. |

The synproxy mss (maximum segment size) and wscale (window scale) settings must match those of your backend server. Synproxy does not attempt to determine these settings; you must find them using tcpdump as demonstrated in the nft manpage in Example ruleset for synproxy statement.

Support for anonymous synproxy was added in nftables 0.9.2.

## Named synproxy

```bash
table ip foo {

    synproxy https-synproxy {
        mss 1460
        wscale 7
        timestamp sack-perm
    }

    synproxy other-synproxy {
        mss 1460
        wscale 5
    }

    chain pre {
        type filter hook prerouting priority raw; policy accept;

        tcp dport 8888 tcp flags syn notrack
    }

    chain bar {
        type filter hook forward priority filter; policy accept;

        ct state invalid,untracked synproxy name ip saddr map {
            192.168.1.0/24 : "https-synproxy", 
            192.168.2.0/24 : "other-synproxy",
        }
    }
}
```

Support for using synproxy named objects was added in nftables 0.9.3.
