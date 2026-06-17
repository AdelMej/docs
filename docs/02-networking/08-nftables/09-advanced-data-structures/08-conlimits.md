# Connlimits

A connlimit in nftables is written as `ct count {over} [count]`. Unlike other stateful object types, all connlimits are anonymous: each connlimit attaches to and applies within the context of a single rule or single element of a dynamic set or map.

A connlimit `ct count {over} [count]`:

- counts the number of current conntrack connections matching its context;
- matches either:
  - only when conntrack is currently tracking fewer than count matching connections, or
  - if over is specified, only when conntrack is currently tracking more than count matching connections.

**Note:** connlimits require at least nftables 0.9.0 and Linux kernel 4.19.10; using connlimits can crash the host when using earlier 4.19.x kernels.

## Using connlimits in rules

```bash
table inet connlimit_demo {
   chain IN { 
      type filter hook input priority filter; policy drop;

      tcp dport 22 ct count 10 accept
   }
}
```

The above ruleset accepts packets to port tcp/22 (sshd), as long as conntrack is currently tracking no more than 10 such sshd connections. If a new SYN to tcp/22 arrives while conntrack already has 10 such connections, it will be dropped.
