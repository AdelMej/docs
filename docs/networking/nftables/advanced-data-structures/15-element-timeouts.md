# Element Timeouts

The set infrastructure supports establishing timeouts. A given timed set element has two attributes:

- **timeout**: This time value (in seconds \[10s\], minutes \[12m\], hours \[2h\], or a combination such as \[2h12m10s\]) does not change and is used to reset the expires value when required.
- **expires**: This value is a countdown time counter which starts with the timeout value; it can be reset from the packet path, or the element will be deleted when it reaches the 0 value.

## Example of per-element timeout

The following commands create a table and a set with the timeout flag, then add an element with a 10-second timeout:

```bash
% nft add table inet myfilter
% nft add set inet myfilter myset {type ipv4_addr; flags timeout; }
% nft add element inet myfilter myset {10.0.0.1 timeout 10s }
```

When listing the ruleset, the `expires` value shows the current countdown:

```nftables
table inet myfilter {
 set myset {
  type ipv4_addr
  flags timeout
  elements = { 10.0.0.1 timeout 10s expires 8s}
 }
}
```

## Resetting Elements

In the case shown above, the `timeout` and `expires` parameters cannot be modified directly. To reset them, the element must be recreated:

```bash
% nft delete element inet myfilter myset { 10.0.0.1 }
% nft add element inet myfilter myset { 10.0.0.1 timeout 7s expires 5s }
```

To be able to reset a timed element from the packet path among other things, you can use the "Updating sets from the packet path" feature.
