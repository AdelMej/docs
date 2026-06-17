# Monitoring ruleset updates

The `nft` utility can display notifications of ruleset updates using the following command:

```bash
nft monitor
```

This command subscribes `nft` to any kind of ruleset update. You can filter these events by:

* **Object:** tables, chains, rules, sets, and elements.
* **Event:** new and destroy.

The output can be formatted in the following ways:

* Plain text (native `nft` format)
* XML
* JSON

### Examples

To track rule updates only:

```bash
nft monitor rules
```

To receive only new rules:

```bash
nft monitor new rules
```

### Advanced Example

You can observe the monitor in action by running `nft monitor` in one terminal and executing commands in another.

**Terminal 1:**

```bash
term1% nft monitor
```

**Terminal 2:**

```bash
term2% nft add table inet filter
term2% nft add chain inet filter forward
term2% nft add rule inet filter forward counter accept
term2% nft flush table inet filter forward
term2% nft flush ruleset
```

**Terminal 1 (Output):**

```bash
term1% nft monitor
add table inet filter
add chain inet filter forward
add rule inet filter forward counter packets 0 bytes 0 accept
delete rule inet filter forward handle 4
delete chain inet filter forward
delete table inet filter
```
