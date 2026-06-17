# Flowtables

Flowtables allow you to accelerate packet forwarding in software (and in hardware if your NIC supports it) by using a conntrack-based network stack bypass. Entries are represented through a tuple composed of the input interface, source and destination addresses, source and destination ports, and layer 3/4 protocols. Each entry also caches the destination interface and the gateway address (to update the destination link-layer address) to forward packets. The TTL and hoplimit fields are also decremented. Thus, flowtables provide an alternative path that allows packets to bypass the classic forwarding path.

```
                                         userspace process
                                          ^              |
                                          |              |
                                     _____|____     ____\/___
                                    /          \   /         \
                                    |   input  |   |  output |
                                    \__________/   \_________/
                                         ^               |
                                         |               |
      _________      __________      ---------     _____\/_____
     /         \    /          \     |Routing |   /            \
  -->  ingress  ---> prerouting ---> |decision|   | postrouting|--> neigh_xmit
     \_________/    \__________/     ----------   \____________/          ^
       |      ^                          |               ^                |
   flowtable  |                     ____\/___            |                |
       |      |                    /         \           |                |
    __\/___   |                    | forward |------------                |
    |-----|   |                    \_________/                            |
    |-----|   |                 'flow add' rule                           |
    |-----|   |                   adds entry to                           |
    |_____|   |                     flowtable                             |
       |      |                                                           |
      / \     |                                                           |
     /hit\_no_|                                                           |
     \ ? /                                                                |
      \ /                                                                 |
       |__yes_________________fastpath bypass ____________________________|

               Fig.1 Netfilter hooks and flowtable interactions
```

Flowtables reside in the ingress hook located before the prerouting hook. You can select which flows you want to offload through the flow expression from the forward chain. Flowtables are identified by their address family and their name; the address family must be one of `ip`, `ip6`, or `inet` (with `ip` being the default).

Flows are offloaded after the state is created, which typically means the first reply packet creates the flowtable entry. A firewall rule to accept the initial traffic is required. The flow expression on the forward chain must match the return traffic of the initial connection. Note that the return route is deducted from the packet when creating the flowtable entry; therefore, if you use special IP rules, you must ensure they match both the reply packet traffic and the original traffic.

### Configuration Details

* **Priority**: Can be a signed integer or `filter` (which represents 0). Addition and subtraction can be used to set relative priority (e.g., `filter + 5` equals 5).
* **Devices**: Specified as `iifname(s)` of the input interface(s) of the traffic to be offloaded. Devices are required for both traffic directions.

### Example: Offloading HTTP Traffic for a Router

The following example demonstrates offloading established HTTP connections:

```nftables
define DEV_PRIVATE=eth0
define DEV_INTERNET=eth1

table inet x {

    flowtable f {
        hook ingress priority 0
        devices = { $DEV_PRIVATE, $DEV_INTERNET }
    }

    chain forward {
        type filter hook forward priority 0; policy drop;

        # offload established HTTP connections
        tcp dport { 80, 443 } flow add @f counter packets 0 bytes 0

        # Allow traffic from established and related packets, drop invalid
        ct state vmap { established : accept, related : accept, invalid : drop }

        # connections from the internal net to the internet or to other
        # internal nets are allowed
        iifname $DEV_PRIVATE counter accept
    }
}
```

**Notes:**

* The rule using the `flow add` statement determines which flows are added to the flowtable. In this example, entries are added for established HTTP connections.
* The devices specified in the flowtable declaration determine where the flowtable hooks in the pipeline for lookups. Here, it registers a hook for devices eth0 and eth1 in the ingress hook at priority 0.
