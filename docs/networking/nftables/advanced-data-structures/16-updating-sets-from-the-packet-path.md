# Updating sets from the packet path

Since nftables v0.7, it is possible to update sets from the packet path, meaning the content of a set can be modified based on the packets the firewall receives. This feature is commonly used in combination with element timeouts to create dynamic blacklists or ban lists.

There are two main operations available: **add** and **update**. They differ in how they handle existing element timeouts:
*   **update**: Refreshes the element timeout for each packet seen.
*   **add**: Does not refresh the timeout of previous elements.

## Example using the update operation
The following example demonstrates using the `update` operation with timeouts to manage a set:

```bash
% nft add table filter
% nft add chain filter input { type filter hook input priority 0\; }
% nft add set filter myset { type inet_service\; flags timeout,dynamic\; }
% nft add rule filter input set update tcp dport timeout 60s @myset
% nft list ruleset
```

**Resulting ruleset:**
```nftables
table ip filter {
        set myset {
                type inet_service
                flags timeout
                elements = { http expires 9s}
        }

        chain input {
                type filter hook input priority 0; policy accept;
                update @myset { tcp dport timeout 1m }
        }
}
```

## Example using the add operation
The following example shows the `add` operation in a set without timeouts:

```bash
% nft add table filter
% nft add chain filter input { type filter hook input priority 0\; }
% nft add set filter myset { type ipv4_addr\; }
% nft add rule filter input set add ip saddr @myset
% nft list ruleset
```

**Resulting ruleset:**
```nftables
table ip filter {
        set myset {
                type ipv4_addr
                elements = { 1.1.1.1 }
        }

        chain input {
                type filter hook input priority 0; policy accept;
                add @myset { ip saddr }
        }
}
```
