# Atomic rule replacement

## Warning about Shell scripting + nftables

With iptables, it was common to use a bash script comprised of multiple iptables commands to configure a firewall. This is sub-optimal because it is not atomic; that is to say, during the few fractions of a second that your bash script takes to run, your firewall is in a partially configured state.

Nftables introduces atomic rule replacement with the `-f` option. This is different from bash scripts because nftables will read in all of the included config files, create the config object in memory alongside the existing config, and then in one atomic operation, it swaps the old config for the new one. This means there is no moment when the firewall is partially configured.

## Atomic Rule Replacement

You can use the `-f` option to atomically update your ruleset:

```bash
nft -f file
```

Where `file` contains your ruleset. You can save your ruleset by storing the existing listing in a file, for example:

```bash
nft list table filter > filter-table
```

Then you can restore it by using the `-f` option:

```bash
nft -f filter-table
```

### Notes

Please take these notes into consideration:

* **Table Creation:** You may have to create the table with `nft create table ip filter` before you can load the file exported with `nft list table filter > filter-table`; otherwise, you will hit errors because the table does not exist. Newer nftables releases behave with more consistency regarding this.
* **Duplicate Rules:** If you prepend the `flush table filter` line at the very beginning of the `filter-table` file, you achieve atomic ruleset replacement equivalent to what `iptables-restore` provides. The kernel handles the rule commands in the file in one single transaction, so basically, the flushing and the load of the new rules happens in one single shot. If you choose not to flush your tables, then you will see duplicate rules for each time you reloaded the config.
* **Flushing Sets:** `flush table filter` will not flush any sets defined in that table. To flush sets as well, use `flush ruleset` (available since Linux 3.17) or delete the sets explicitly. Early versions (Linux <=3.16) do not allow you to import a set if it already exists, but this is allowed in later versions.
* **Including multiple files:** If you have two included files both with statements for the filter table (e.g., one adds a rule for 192.168.1.1 and the other for 192.168.1.2), then both rules will be included in the chain, even if one or both files contains a flush statement.
* **Flush statements in included files:** If there are any flush commands in any included file, then those will be run at the moment the config swap is executed, not at the moment the file is loaded. If you do not include a flush statement in any included file, you will get duplicate rules. If you do include a flush statement, you will not get duplicate rules and the config from *both* files will be included.
