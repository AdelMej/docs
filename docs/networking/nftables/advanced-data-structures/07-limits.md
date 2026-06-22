# Limits

A limit uses a token bucket filter to match packets based on whether its rate is exceeded:
- only until its rate is exceeded; or
- only after its rate is exceeded, if defined as an over limit.

## Anonymous limits

## Named limits

### Declaring and using named limits
```bash
table inet limit_demo {

   limit lim_400ppm { rate 400/minute ; comment "use to limit incoming icmp" ; }
   limit lim_1kbps  { rate over 1024 bytes/second burst 512 bytes ; comment "use to limit incoming smtp" ; }

   chain IN { 
      type filter hook input priority filter; policy drop;

      meta l4proto icmp limit name "lim_400ppm" accept
      tcp dport 25 limit name "lim_1kbps" accept
   }
}
```
The above ruleset defines a per-packet named limit `lim_400ppm` and a per-byte named limit `lim_1kbps`. The rules in input chain IN use these named limits to:

- Accept icmp packets, of all icmp types, up to a maximum rate of 400 packets / minute.
- Accept traffic to port tcp/25 (smtp), up to a maximum rate of 1024 bytes / second. Up to 512 bytes of such traffic arriving faster than this is accepted.
- Drop all other traffic.

The optional comment attribute requires at least nftables 0.9.7 and kernel 5.10.

### Listing named limits
`nft list [limit | limits]` (as per below) returns the limit(s) with current byte count.

List a particular limit:
```bash
% nft list limit [family] [table_name] [limit_name]
```
List all limits in a particular table:
```bash
% nft list limits table [family] [table_name]
```
List all limits in ruleset:
```bash
% nft list limits
```
