# Counters

A counter counts both the total number of packets and the total bytes it has seen since it was last reset. With nftables, you need to explicitly specify a counter for each rule you want to count.

## Anonymous counters

An anonymous counter is local to the single rule in which it appears. The following example uses an anonymous counter to count all tcp traffic routed to the local host:

```nftables
table ip counter_demo {
    chain IN {
        type filter hook input priority filter; policy drop;

        ip protocol tcp counter
    }
}
```

Note that the position of the counter statement within your rule is significant, because nftables evaluates expressions and statements linearly from left to right. If the above rule were written instead:

```nftables
counter ip protocol tcp
```

then every packet routed to your host (not just tcp packets) will update the counter!

## Named counters

### Declaring and using named counters

You can also declare named counters, which can be used in multiple rules, e.g.:

```nftables
table inet named_counter_demo {

    counter cnt_http {
        comment "count both http and https packets"
    }

    counter cnt_smtp {
    }

    chain IN {
        type filter hook input priority filter; policy drop;

        tcp dport   25 counter name cnt_smtp
        tcp dport   80 counter name cnt_http
        tcp dport  443 counter name cnt_http
   }
}
```

The above example defines two counters named `cnt_http` and `cnt_smtp` and uses them in rules to count http(s) and smtp packets routed to the local host. (This example is contrived to show using a single named counter in multiple rules; the two rules using `cnt_http` can easily be combined by using an anonymous set.) The optional comment attribute requires at least nftables 0.9.7 and kernel 5.10.

### Listing / reading named counters

#### Listing named counters from nft command line

`nft list [counter | counters]` (as per below) returns the current value(s) of the selected counter(s).

List a particular counter:
`% nft list counter inet named_counter_demo cnt_http`

List all counters in a particular table:
`% nft list counters table inet named_counter_demo`

List all counters in ruleset:
`% nft list counters`

#### Reading named counters from Python

The following partial ruleset (note the absence of a base chain) defines two named counters `voip1` and `voip2` and uses them to count VoIP traffic to udp/5160 and udp/5161. The commented-out rules show how to do this in simple fashion, while the 2 final rules in the FORWARD chain do the same thing using the `voipcounters` map. The approach using the map becomes increasingly advantageous when more ports (map elements) are added.

```nftables
define ipvoipbox=192.168.0.8

table ip filter {
    counter voip1 {
    }
    counter voip2 {
    }
    map voipcounters {
        type inet_service : counter
        elements = {
            5160 : "voip1", 
            5161 : "voip2" 
        }
    }
    chain FORWARD {
        #ip saddr $ipvoipbox udp dport 5160 counter name voip1 comment "counting packets for SIP1"
        #ip daddr $ipvoipbox udp dport 5160 counter name voip1 comment "counting packets for SIP1"
        #ip saddr $ipvoipbox udp sport 5161 counter name voip2 comment "counting packets for SIP2"
        #ip daddr $ipvoipbox udp sport 5161 counter name voip2 comment "counting packets for SIP2"
        ip saddr $ipvoipbox counter name udp sport map @voipcounters
        ip daddr $ipvoipbox counter name udp dport map @voipcounters
    }
 }
```

We can read current counter values from Python using the libnftables library:

```python
from nftables import Nftables
from nftables import json

def getCounter(countername, family='ip'):
    nft = Nftables()
    nft.set_json_output(True)
    _, output, _ = nft.cmd(f"list counter {family} filter {countername}")
    j = json.loads(output)
    return j['nftables'][1]["counter"]["bytes"]

print(getCounter('voip1'), 'bytes')
print(getCounter('voip2'), 'bytes')
```

### Resetting named counters

Resetting a counter dumps its current packet and byte counts and then resets the counts to their initial values.

Reset a particular counter:
`% nft reset counter inet named_counter_demo cnt_http`

Reset all counters in a particular table:
`% nft reset counters table inet named_counter_demo`

Reset all counters in ruleset:
`% nft reset counters`

Note: Resetting counters does not reset anonymous counters, see bug #1401. A workaround to achieve that is to restore the current ruleset with all stateful information dropped:

`% (echo "flush ruleset"; nft --stateless list ruleset) | nft -f -`

Obviously, this drops all state so might have undesired side-effects, like, e.g. resetting quotas.
