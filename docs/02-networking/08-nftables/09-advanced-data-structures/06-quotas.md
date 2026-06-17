# Quotas

A quota:

- defines a threshold number of bytes;
- sets an initial byte count (defaults to 0 bytes if not specified);
- counts the total number of bytes, starting from the initial count; and
- matches either:
  - only until the byte count exceeds the threshold, or
  - only after the byte count is over the threshold.

## Anonymous quotas

An anonymous quota is local to the single rule in which it appears. The following example uses an anonymous quota to allow only up to 100 mbytes to port udp/5060:

```bash
table inet anon_quota_demo {
    chain IN {
        type filter hook input priority filter; policy drop;

        udp dport 5060 quota until 100 mbytes accept
    }
}
```

## Named quotas

### Declaring and using named quotas

You can also declare named quotas, which can be used in multiple rules and maps (only as values, not as keys), as well as reset. For example:

```bash
table inet quota_demo {
   quota q_until_sip { until 100 mbytes used 0 bytes }
   quota q_over_http { over  500 mbytes ; comment "cap http (but not https)" ; }

   chain IN { 
      type filter hook input priority filter; policy drop;

      udp dport 5060 quota name "q_until_sip" accept
      tcp dport 80 quota name "q_over_http" drop
      tcp dport { 80, 443 } accept
   }
}
```

The above ruleset defines a couple named quotas, each with initial count of 0 bytes. The rules in input chain IN use these named quotas to:

- accept only up to 100 mbytes total to udp/5060, then drop any additional packets to this (sip) port;
- accept only up to 500 mbytes total to tcp/80, then drop any additional packets to this (http) port;
- accept unlimited packets to tcp/443 (https);
- drop any other packets (note drop policy).

The optional comment attribute requires at least nftables 0.9.7 and kernel 5.10.

### Listing named quotas

`nft list [quota | quotas]` returns the quota(s) with current byte count.

List a particular quota:

```bash
% nft list quota inet quota_demo q_over_http
```

List all quotas in a particular table:

```bash
% nft list quotas table inet quota_demo
```

List all quotas in ruleset:

```bash
% nft list quotas
```

### Resetting named quotas

Resetting a quota dumps its current byte count and then resets the byte count to its initial value.

Reset a particular quota:

```bash
% nft reset quota inet quota_demo q_until_sip
```

Reset all quotas in a particular table:

```bash
% nft reset quotas table inet quota_demo
```

Reset all quotas in ruleset:

```bash
% nft reset quotas
```

Note: Resetting quotas does not reset anonymous quotas, see bug #1314.
