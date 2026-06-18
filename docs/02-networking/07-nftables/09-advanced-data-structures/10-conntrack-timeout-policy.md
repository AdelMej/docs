# Ct timeout

You can use a ct timeout object to specify a connection tracking timeout policy for a particular flow.

The partial ruleset below defines a ct timeout named agressive-tcp and then attaches it to a rule to override the default timeout policy for packets going to port tcp/8888:

```nftables
table inet ct_timeout_demo {

    ct timeout agressive-tcp {
        l3proto ip;
        protocol tcp;
        policy = {established: 100, close_wait: 4, close: 4}
    }

    chain output {
        tcp dport 8888 ct timeout set "agressive-tcp"
    }
}
```

Support for ct timeout was added in nftables 0.9.1.
