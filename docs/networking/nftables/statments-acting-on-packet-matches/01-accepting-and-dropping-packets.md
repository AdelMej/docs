# Accepting and dropping packets

## Dropping packets
You can use the `drop` option to drop packets. Note that `drop` is a terminating action, so you cannot add any other action after it.

```bash
nft add rule filter output drop
```

Beware when testing this, you'll likely lose any Internet connectivity :-).

## Accepting packets
A simple rule to accept any sort of traffic is:

```bash
nft add rule filter output accept
```

You can add counters to that rule:

```bash
nft add rule filter output counter accept
```

So you can watch that all traffic is actually accepted:

```text
nft list table filter
table ip filter {
        chain output {
                 type filter hook output priority 0;
                 counter packets 1 bytes 84 accept
        }
}
```
