# Logging traffic

Note: Full logging support is available starting Linux kernel 3.17. If you run an older kernel, you have to modprobe `ipt_LOG` to enable logging.

You can log packets using the `log` action. The most simple rule to log all incoming traffic is:

```bash
% nft add rule filter input log
```

A typical rule match, log and accept incoming ssh traffic looks like:

```bash
% nft add rule filter input tcp dport 22 ct state new log prefix \"New SSH connection: \" accept
```

The prefix indicates the initial string that is used as prefix for the log message.

Note that nftables allows to perform two actions in one single rule, contrary to iptables which required two rules for this. Also note that the rule is evaluated from the left to the right. So the following rule:

```bash
nft add rule filter input iif lo log tcp dport 22 accept
```

will log all packets coming on lo interface and not only the ones with destination port 22.

## Queueing logging to userspace

As in iptables, you can use the existing nflog infrastructure to send log messages to ulogd or your custom userspace application based on libnetfilter_log. To do so, you only have to indicate the nflog group:

```bash
% nft add rule filter input tcp dport 22 ct state new log prefix \"New SSH connection: \" group 0 accept
```

Then, run the example test application:

```bash
libnetfilter_log/utils% ./nfulnl_test
```

And you'll start seeing log messages for each new ssh connection.

## Log flags

Since nftables v0.7, log flags are supported.

Enable logging of TCP sequence and options:

```bash
% nft add rule x y log flags tcp sequence,options
```

Enable IP options:

```bash
% nft add rule x y log flags ip options
```

Enable socket UID:

```bash
% nft add rule x y log flags skuid
```

Enable ethernet link layer address:

```bash
% nft add rule x y log flags ether
```

Enable all flags:

```bash
% nft add rule x y log flags all
```

## Additional options

Some additional options exist to fine-tune logging in different scenarios:

* **level**: Syslog level of logging, a string of value: `emerg`, `alert`, `crit`, `err`, `warn` [default], `notice`, `info`, `debug`
* **snaplen**: Length of packet payload to include in netlink message (unsigned integer, 32 bit)
* **queue-threshold**: If queing logging to userspace, number of packets to queue inside the kernel before sending them to userspace (unsigned integer, 32 bit)

In the example below, a queue-threshold parameter is used to increment ulogd2 daemon performance in userspace:

```bash
% nft add rule filter forward ct state invalid log queue-threshold 20 prefix "ct_invalid" group 0 drop
```
