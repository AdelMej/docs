# Ct expectation

You can use a ct expectation object to create a connection tracking expectation. The ruleset below creates an expectation on destination port tcp/5432 for each new connection to port tcp/8888. This expectation expires after 1 hour and the maximum number of expectations that are pending to be confirmed are 12.

```nftables
table ct_expect_demo {

    ct expectation e_pgsql {
        protocol tcp
        dport 5432
        timeout 1h
        size 12
        l3proto ip
    }

    chain input {
        type filter hook input priority filter;

        ct state new tcp dport 8888 ct expectation set "e_pgsql"
        ct state established,related counter accept
    }
}
```

Support for ct expectation was added in nftables 0.9.2.
