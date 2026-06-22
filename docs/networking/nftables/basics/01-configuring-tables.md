# Configuring Tables

Tables are the top-level containers within an nftables ruleset; they hold chains, sets, maps, flowtables, and stateful objects. Each table belongs to exactly one family, so a ruleset requires at least one table for each family you want to filter.

## Basic Operations

### Adding tables

```bash
nft add table ip filter
```

### Show/List tables

```bash
nft list tables
```

### Deleting tables

```bash
nft delete table ip foo
```

* **Troubleshooting:** Since Linux kernel 3.18, you can delete a table and its contents with this command. Earlier kernels require you to flush the table's contents first to avoid "Device or resource busy" errors.

### Flushing tables

```bash
nft flush table ip filter
```

* This removes rules for every chain registered in that table.
* **Note:** This will not flush Sets defined within the table. On Linux <4.9.0, this may cause an error if the table does not exist.

**Summary:** The document provides basic commands and troubleshooting for managing nftables tables, including adding, listing, deleting, and flushing rules across different families.
