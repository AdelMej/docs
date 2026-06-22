# Secmark

Secmark objects add netfilter SECMARK labels to a ruleset, for use with SELinux or other Linux Security Modules. At least Linux kernel 4.20 and nftables 0.9.3 are required for secmark object support.

## Using secmark in rules

The following ruleset defines an sshtag secmark object and uses it to set SECMARK on packets to port tcp/22 (ssh):

```nftables
table inet secmark_rule_demo {

    secmark sshtag { "system_u:object_r:ssh_server_packet_t:s0" }

    chain IN {
        type filter hook input priority filter;

        tcp dport 22 meta secmark set "sshtag"
    }
}
```

## Using secmark in maps

You can also use secmark in maps:

```nftables
table inet secmark_map_demo {

    secmark sshtag { "system_u:object_r:ssh_server_packet_t:s0" }

    map secmapping {
        type inet_service : secmark
        elements = {
            22 : "sshtag",
        }
    }

    chain IN {
        type filter hook input priority filter;

        meta secmark set tcp dport map @secmapping
    }
}
```
