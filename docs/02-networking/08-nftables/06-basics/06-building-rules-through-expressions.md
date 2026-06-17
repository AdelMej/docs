# Building rules through expressions

nftables provides the following built-in operations:

* **eq**: stands for equal. Alternatively, you can use `==`.
* **ne**: stands for not equal. Alternatively, you can use `!=`.
* **lt**: stands for less than. Alternatively, you can use `<`.
* **gt**: stands for greater than. Alternatively, you can use `>`.
* **le**: stands for less than or equal to. Alternatively, you can use `<=`.
* **ge**: stands for greater than or equal to. Alternatively, you can use `>=`.

**Warning:** If you use the symbols `<` and `>` from the shell, the shell will interpret those as standard input and output redirection respectively. You will need to escape them (e.g., `\<`).

The following example shows how to match all incoming traffic not coming to port TCP/22:

```bash
nft add rule filter input tcp dport != 22
```

Similarly, you can also match traffic coming to high ports with the following command:

```bash
nft add rule filter input tcp dport >= 1024
```
