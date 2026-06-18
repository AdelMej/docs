# Queueing to userspace

## Basic operation

**Important note:** You require a Linux kernel 3.14 to enqueue packets to userspace using nftables.

Like in iptables, you can use the `nfqueue` infrastructure to enqueue packets to your userspace application that uses the `libnetfilter_queue` library.

You can test this with the example application:

```bash
libnetfilter_queue/utils% ./nfqnl_test
```

After that, you have to add the rule to enqueue packets to userspace. If no queue is specified, the packets are sent to the queue number 0:

```bash
nft add filter input counter queue
```

Then, you should start seeing packets when generating some traffic:

```text
...
pkt received
hw_protocol=0x0800 hook=1 id=28 hw_src_addr=00:80:48:52:ff:8a indev=3 uid=1000 gid=1000 payload_len=110 
entering callback
pkt received
hw_protocol=0x0800 hook=1 id=29 hw_src_addr=00:80:48:52:ff:8a indev=3 uid=1000 gid=1000 payload_len=98 
entering callback
...
```

There are up to 65535 queues. You can select a different queue with the following command:

```bash
nft add filter input counter queue num 3
```

Then, you have to launch the test application including the argument that indicates the queue number to listen for packets:

```bash
libnetfilter_queue/utils% ./nfqnl_test 3
```

**Important note:** If there is no userspace application listening to that queue, then all packets will be dropped.

## A bit more advanced configuration

You can also enable the **bypass** option which will skip the enqueue of the packet to userspace if no application is listening to the queue. The rule will behave as an accept rule if there is no application waiting for packets:

```bash
nft add filter input counter queue num 0 bypass
```

You can also load balance traffic to several queues:

```bash
nft add filter input counter queue num 0-3
```

Thus, the queue numbers from 0 to 3 will be used. You can run four instances of `nfqnl_test` to test this:

```bash
libnetfilter_queue/utils% ./nfqnl_test 0 &
libnetfilter_queue/utils% ./nfqnl_test 1 &
libnetfilter_queue/utils% ./nfqnl_test 2 &
libnetfilter_queue/utils% ./nfqnl_test 3 &
```

When doing load balancing, you can optionally use the **fanout** option to use the CPU ID as an index to map packets to the queues. The idea is that you can improve performance if there's a queue/userspace application per CPU:

```bash
nft add filter input counter queue num 0-3 fanout
```

Of course, the options can be combined, so you can for example use:

```bash
nft add filter input counter queue num 0-3 fanout,bypass
```
