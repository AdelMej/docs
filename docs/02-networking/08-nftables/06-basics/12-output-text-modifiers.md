# Output text modifiers

This page contains information on the several output text modifiers that nftables support when using the command line interface `nft`. You can generally check all the output modifiers by using `nft --help` or reading the manpage `nft(8)`.

* **-n, numeric**: Print fully numerical output.
* **-s, stateless**: Omit stateful information of ruleset.
* **-N, reversedns**: Translate IP addresses to names.
* **-S, service**: Translate ports to service names as described in `/etc/services`.
* **-a, handle**: Output rule handle.
* **-j, json**: Format output in JSON.
* **-u, guid**: Print UID/GID as defined in `/etc/passwd` and `/etc/group`.
* **-y, numeric-priority**: Print chain priority numerically.
* **-p, numeric-protocol**: Print layer 4 protocols numerically.
* **-T, numeric-time**: Print time values numerically.
* **-t, terse**: Omit contents of sets.

The default output prints some information in numeric form and for well-known names it will use a string instead (like icmp types, conntrack status, chain priorities, etc). Also, state information such as counter values and set elements are printed as well.

```text
% nft list ruleset
table inet filter {
 set s {
  type inet_service
  elements = { 80, 443 }
 }

 chain input {
  type filter hook input priority filter; policy accept;
  counter packets 4447 bytes 1619415
  iif "lo" counter packets 337 bytes 25076 accept
  ct state established,related counter packets 44899 bytes 106405802 accept
  ip6 nexthdr ipv6-icmp icmpv6 type { nd-router-advert, nd-neighbor-solicit, nd-neighbor-advert } counter packets 1 bytes 72 accept
  tcp dport 22 drop
  ip saddr 8.8.8.8 drop
 }
}
```

## Translation modifiers

Translate various values to text equivalents, or the other way around. We can group here things like ports, DNS names, service names, UID/GID, etc. The options can be combined at will. The example below shows service names (instead of the integer number), chain priority value (instead of the well-known string), conntrack/protocol numbers and constants (instead of well-known strings) and shows reverse DNS names (instead of the numeric IP address):

```text
% nft -nNSy list ruleset
table inet filter {
 set s {
  type inet_service
  elements = { "http", "https" }
 }

 chain input {
  type filter hook input priority 0; policy accept;
  iif "lo" counter packets 365 bytes 27092 accept
  ct state 0x2,0x4 counter packets 48535 bytes 142472901 accept
  ip6 nexthdr 58 icmpv6 type { 134, 135, 136 } counter packets 1 bytes 72 accept
  ip saddr dns.google counter packets 0 bytes 0
  tcp dport "ssh" accept
 }
}
```

Note that translating some elements might take additional operation time when generating the output. For example translating IP addresses to names require queries to DNS servers, which can be very slow for large rulesets (and therefore is disabled by default).

## Operations and parsing modifiers

These modifiers add or remove information about the ruleset, generally useful when parsing the output or doing related operations. You can display the ruleset without stateful information (for example, without counter values), with handles, and with no set contents:

```text
% nft -sta list ruleset
table inet filter { # handle 5
 set s { # handle 9
  type inet_service
 }

 chain input { # handle 1
  type filter hook input priority filter; policy accept;
  iif "lo" counter accept # handle 3
  ct state established,related counter accept # handle 4
  ip6 nexthdr ipv6-icmp icmpv6 type { nd-router-advert, nd-neighbor-solicit, nd-neighbor-advert } counter accept # handle 5
  ip saddr 8.8.8.8 counter # handle 8
  tcp dport 22 accept # handle 10
 }
}
```

### Special mention to the JSON representation of the ruleset

The JSON will be printed in a single line fashion. Here we format the JSON using perl's `json_pp` utility:

```text
% nft -j list ruleset | json_pp
{
   "nftables" : [
      {
         "metainfo" : {
            "json_schema_version" : 1,
            "release_name" : "Capital Idea #2",
            "version" : "0.9.6"
         }
      },
      {
         "table" : {
            "family" : "inet",
            "handle" : 5,
            "name" : "filter"
         }
      },
      {
         "set" : {
            "elem" : [
               80,
               443
            ],
            "family" : "inet",
            "handle" : 9,
            "name" : "s",
            "table" : "filter",
            "type" : "inet_service"
         }
      },
      {
         "chain" : {
            "family" : "inet",
            "handle" : 1,
            "hook" : "input",
            "name" : "input",
            "policy" : "accept",
            "prio" : 0,
            "table" : "filter",
            "type" : "filter"
         }
      },
      {
         "rule" : {
            "chain" : "input",
            "expr" : [
               {
                  "counter" : {
                     "bytes" : 37707381,
                     "packets" : 8062
                  }
               }
            ],
            "family" : "inet",
            "handle" : 7,
            "table" : "filter"
         }
      },
[..]
```
