# Operations at ruleset level

## Using native nft syntax

Linux Kernel 3.18 includes some improvements regarding the available operations to manage your ruleset as a whole.

### listing

Listing the complete ruleset:

```bash
nft list ruleset
```

Listing the ruleset per family:

```bash
nft list ruleset arp
nft list ruleset ip
nft list ruleset ip6
nft list ruleset bridge
nft list ruleset inet
```

These commands will print all tables/chains/sets/rules of the given family.

### flushing

In addition, you can also flush (erase, delete, wipe) the complete ruleset:

```bash
nft flush ruleset
```

Also per family:

```bash
nft flush ruleset arp
nft flush ruleset ip
nft flush ruleset ip6
nft flush ruleset bridge
nft flush ruleset inet
```

### backup/restore

You can combine these two commands above to backup your ruleset:

```bash
echo "flush ruleset" > backup.nft
nft list ruleset >> backup.nft
```

And load it atomically:

```bash
nft -f backup.nft
```

## Listing in JSON format

You can also export your ruleset in JSON format, just pass the `--json` option:

```bash
nft --json list ruleset > ruleset.json
```
