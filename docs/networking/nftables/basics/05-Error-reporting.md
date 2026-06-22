# Error reporting from the command line

The `nft` command line utility tries to help you when you use a wrong datatype.

The following example shows the error output if you pass an IPv4 address as a TCP port:

```bash
% nft add rule filter input tcp dport 1.1.1.1 counter drop
<cmdline>:1:33-39: Error: Could not resolve service: Servname not supported for ai_socktype
add rule filter input tcp dport 1.1.1.1 counter drop
                                ^^^^^^^
```

The typical output if your command is incomplete is the following:

```bash
% nft add rule filter input tcp dport
<cmdline>:1:32-32: Error: syntax error, unexpected end of file
add rule filter input tcp dport
                               ^
```
