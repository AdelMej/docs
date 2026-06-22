# Math operations

## Math operations

nftables includes some interesting math operations generators which can be used to perform advanced operations like Load balancing.

### Number generator

The number generator statement has these options:

* **type:** inc/random
* **modulus:** maximum number
* **offset:** from what value you want to start from

Some examples, distributing marks to packets:

```bash
table ip t {
 chain c {
  mark set numgen inc mod 4 offset 3
  mark set numgen random mod 50 offset 20
  mark set numgen inc mod 100
 }
}
```

The statement `numgen inc mod 2 offset 100` will generate numbers in a series like 100, 101, 100, 101...

### Hashing

nftables support hashing of any arbitrary key combination:

```bash
table ip t {
 chain c {
  mark set jhash ip saddr mod 2
  mark set jhash ip saddr . tcp dport mod 2
  mark set jhash ip saddr . tcp dport . iiftype mod 2
 }
}
```
