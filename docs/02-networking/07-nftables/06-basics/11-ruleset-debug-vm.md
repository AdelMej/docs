# Ruleset debug/VM code analysis

In the kernel, `nf_tables` is implemented as a virtual machine with its own instruction set. The kernel's expressions implement such instructions. In user space, the mapping is not necessarily as direct as this.

## Statements and expressions in user space

In user space nomenclature, a distinction is made between statements and expressions; the relevant difference is that the former are valid parts of a rule on their own while the latter usually appear as parameter or input to a statement. For instance, take the following payload statement:

`ip dscp set 42`

Here, `ip dscp` is an expression identifying what part of the packet payload to mangle, and `42` is a constant expression holding the value to assign. There are certain limits as to what may appear after the `set` keyword; `nft` does some type checking to make sure it is compatible. To illustrate the power this concept has, take the following example:

`tcp dport set tcp sport`

This will mangle a TCP packet's destination port to match whatever its source port value may be. Albeit a bit constructed, this is an example of a statement accepting data from two expressions.

## Expressions in kernel space

The kernel does not have the concept of a statement. There are merely expressions (instructions) loading data from or writing to registers, thereby interacting with each other. Aside from twenty general purpose data registers (each sized four bytes), there is a single verdict register used to terminate rule or even chain traversal available to expressions.

## VM bytecode in action

When `nft` translates user input into VM code for the kernel, it translates from statements and expressions as user space knows them into VM code calling kernel's expressions. Passing the `--debug=netlink` option makes this visual:

```text
nft --debug=netlink add rule inet t c ip daddr 10.1.2.3 counter accept
inet t c
  [ meta load nfproto => reg 1 ]
  [ cmp eq reg 1 0x00000002 ]
  [ payload load 4b @ network header + 16 => reg 1 ]
  [ cmp eq reg 1 0x0302010a ]
  [ counter pkts 0 bytes 0 ]
  [ immediate reg 0 accept ]
```

The rule added by the command above matches the packet's IPv4 Destination Address field against the value `10.1.2.3`, counts matching packets, and finally accepts them (thereby ending chain traversal for this packet).

The printed VM bytecode reveals a few more interesting details:

* **The rule actually starts with a match on meta nfproto value:** Because the chain resides in the `inet` family, it may see packets other than IPv4 ones as well. This filtering is necessary because user space's `ip daddr` expression is actually very generic: The payload expression it translates into merely loads 4B from the network header at offset 16B into register 1. With an IPv6 packet, this would happily load parts of the Source Address field, which might even match the value `0x0302010a` by accident. To avoid such unexpected (and unforeseeable) behaviour, the implicit meta nfproto match is required.
* **Packet matching is often implicit:** The second expression, comparing meta nfproto value against `0x02` (= NFPROTO_IPV4) for equality (eq). If not successful, it will write the value `NFT_BREAK` into the verdict register and chain traversal continues with the next rule.
* **Explicit Verdict Access:** Contrary to the above, the last expression is an explicit access to the verdict register (reg 0), writing `NF_ACCEPT`.

## Statements in VM bytecode

Picking up one of the examples above:

```text
nft --debug=netlink add rule t c tcp dport set tcp sport
ip t c
  [ meta load l4proto => reg 1 ]
  [ cmp eq reg 1 0x00000006 ]
  [ payload load 2b @ transport header + 0 => reg 1 ]
  [ payload write reg 1 => 2b @ transport header + 2 csum_type 1 csum_off 16 csum_flags 0x0 ]
```

This reveals how a payload statement is actually just a variant of the kernel's 'payload expression' which writes instead of reads. Some further observations:

* There is again an implicit match present, this time to assert meta l4proto matches the value `0x6` (= IPPROTO_TCP). Again, this is to make sure the following payload expressions don't read garbage or write to odd parts of the packet.
* The second 'payload expression' also holds extra information for a partial checksum update which is necessary after mangling an IPv4 packet.
