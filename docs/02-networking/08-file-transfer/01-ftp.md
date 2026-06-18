# File Transfer Protocol

The File Transfer Protocol (FTP) is a standard communication protocol used for the transfer of computer files from a server to a client over a computer network. FTP is built on a client–server model architecture using separate control and data connections between the client and the server.

FTP users may authenticate themselves with a plain-text sign-in protocol, normally in the form of a username and password, but can connect anonymously if the server is configured to allow it. For secure transmission that protects the username and password, and encrypts the content, FTP is often secured with SSL/TLS (FTPS) or replaced with SSH File Transfer Protocol (SFTP).

The first FTP client applications were command-line programs developed before operating systems had graphical user interfaces, and are still shipped with most Windows and Unix-like operating systems. Many dedicated FTP clients and automation utilities have since been developed for desktops, servers, mobile devices, and hardware, and FTP has been incorporated into productivity applications such as HTML editors and file managers.

An FTP client used to be commonly integrated in web browsers, where file servers are browsed with the URI prefix "ftp://". In 2021, FTP support was dropped by Google Chrome and Firefox, two major web browser vendors, due to it being superseded by the more secure SFTP and FTPS; although neither browser has implemented the newer protocols.

## History of FTP Servers

The original specification for the File Transfer Protocol was written by Abhay Bhushan and published as RFC 114 on 16 April 1971. Until 1980, FTP ran on NCP, the predecessor of TCP/IP. The protocol was later replaced by a TCP/IP version, RFC 765 (June 1980) and RFC 959 (October 1985), which is the current specification. Several proposed standards amend RFC 959, for example:

* RFC 1579 (February 1994) enables Firewall-Friendly FTP (passive mode).
* RFC 2228 (June 1997) proposes security extensions.
* RFC 2428 (September 1998) adds support for IPv6 and defines a new type of passive mode.

## Protocol Overview

### Communication and Data Transfer

FTP may run in active or passive mode, which determines how the data connection is established.

**Active Mode:**
The client starts listening for incoming data connections from the server on port M. It sends the FTP command `PORT M` to inform the server on which port it is listening. The server then initiates a data channel to the client from its port 20, the FTP server data port.

**Passive Mode:**
In situations where the client is behind a firewall and unable to accept incoming TCP connections, passive mode may be used. In this mode, the client uses the control connection to send a `PASV` command to the server and then receives a server IP address and server port number from the server, which the client then uses to open a data connection from an arbitrary client port to the server IP address and server port number received.

Both modes were updated in September 1998 to support IPv6. Further changes were introduced to the passive mode at that time, updating it to extended passive mode.

The server responds over the control connection with three-digit status codes in ASCII with an optional text message. For example, "200" (or "200 OK") means that the last command was successful. An ongoing transfer of file data over the data connection can be aborted using an interrupt message sent over the control connection.

FTP needs two ports (one for sending and one for receiving) because it was originally designed to operate on top of Network Control Protocol (NCP), which was a simplex protocol that utilized two port addresses, establishing two connections, for two-way communications. An odd and an even port were reserved for each application layer application or protocol. The standardization of TCP and UDP reduced the need for the use of two simplex ports for each application down to one duplex port, but the FTP protocol was never altered to only use one port, and continued using two for backwards compatibility.

### NAT and Firewall Traversal

FTP normally transfers data by having the server connect back to the client after the `PORT` command is sent by the client. This is problematic for both NATs and firewalls, which do not allow connections from the Internet towards internal hosts. For NATs, an additional complication is that the representation of the IP addresses and port number in the `PORT` command refer to the internal host's IP address and port, rather than the public IP address and port of the NAT.

There are two approaches to solving this problem:

1. The FTP client and FTP server use the `PASV` command, which causes the data connection to be established from the FTP client to the server. This is widely used by modern FTP clients.
2. The NAT alters the values of the `PORT` command, using an application-level gateway for this purpose.

### Data Types

While transferring data over the network, five data types are defined:

* **ASCII (TYPE A):** Used for text. Data is converted, if needed, from the sending host's character representation to "8-bit ASCII" before transmission, and (again, if necessary) to the receiving host's character representation, including newlines.
* **Image (TYPE I, commonly called Binary mode):** The sending machine sends each file byte by byte, and the recipient stores the bytestream as it receives it.
* **EBCDIC (TYPE E):** Used for plain text between hosts using the EBCDIC character set.
* **Local (TYPE L n):** Designed to support file transfer between machines which do not use 8-bit bytes, e.g., 36-bit systems such as DEC PDP-10s. Most contemporary FTP clients/servers only support L 8, which is equivalent to I.
* **Unicode text files using UTF-8 (TYPE U):** Defined in an expired Internet Draft which never became an RFC, though it has been implemented by several FTP clients/servers.

For text files (TYPE A and TYPE E), three different format control options are provided:

* **Non-print (TYPE A N and TYPE E N):** The file does not contain any carriage control characters intended for a printer.
* **Telnet (TYPE A T and TYPE E T):** The file contains Telnet (or in other words, ASCII C0) carriage control characters (CR, LF, etc.).
* **ASA (TYPE A A and TYPE E A):** The file contains ASA carriage control characters.

### File Structures

File organization is specified using the `STRU` command. The following file structures are defined:

* **F or FILE structure (stream-oriented):** Files are viewed as an arbitrary sequence of bytes, characters or words. This is the usual file structure on Unix systems and other systems such as CP/M, MS-DOS and Microsoft Windows.
* **R or RECORD structure (record-oriented):** Files are viewed as divided into records, which may be fixed or variable length. This file organization is common on mainframe and midrange systems, such as MVS, VM/CMS, OS/400 and VMS.
* **P or PAGE structure (page-oriented):** Files are divided into pages, which may either contain data or metadata; each page may also have a header giving various attributes. This file structure was specifically designed for TENEX systems and is generally not supported on other platforms.

### Data Transfer Modes

Data transfer can be done in any of three modes:

* **Stream mode (MODE S):** Data is sent as a continuous stream, relieving FTP from doing any processing. All processing is left up to TCP.
* **Block mode (MODE B):** Designed primarily for transferring record-oriented files (STRU R), although it can also be used to transfer stream-oriented (STRU F) text files. FTP puts each record (or line) of data into several blocks (block header, byte count, and data field) and then passes it on to TCP.
* **Compressed mode (MODE C):** Extends MODE B with data compression using run-length encoding.

## Login

FTP login uses normal username and password scheme for granting access. The username is sent to the server using the `USER` command, and the password is sent using the `PASS` command. This sequence is unencrypted "on the wire", so may be vulnerable to a network sniffing attack. If the information provided by the client is accepted by the server, the server will send a greeting to the client and the session will commence. If the server supports it, users may log in without providing login credentials, but the same server may authorize only limited access for such sessions.

### Anonymous FTP

A host that provides an FTP service may provide anonymous FTP access. Users typically log into the service with an 'anonymous' account when prompted for user name. Although users are commonly asked to send their email address instead of a password, no verification is actually performed on the supplied data. Many FTP hosts whose purpose is to provide software updates will allow anonymous logins.

## Software Support

### File managers

Many file managers tend to have FTP access implemented, such as File Explorer (formerly Windows Explorer) on Microsoft Windows. This client is only recommended for small file transfers from a server, due to limitations compared to dedicated client software. It does not support SFTP. Both the native file managers for KDE on Linux (Dolphin and Konqueror) support FTP as well as SFTP. On Android, the My Files file manager on Samsung Galaxy has a built-in FTP and SFTP client.

### Web browser

For a long time, most common web browsers were able to retrieve files hosted on FTP servers. Google Chrome removed FTP support entirely in Chrome 88, also affecting other Chromium-based browsers such as Microsoft Edge. Firefox 88 disabled FTP support by default, with Firefox 90 dropping support entirely. Some browsers, such as the text-based Lynx, still support FTP.

**Syntax:**
FTP URL syntax is described in RFC 1738, taking the form: `ftp://user:password@host:port/path`. Only the host is required. By default, most web browsers use passive (PASV) mode, which more easily traverses end-user firewalls.

### Download manager

Most common download managers can receive files hosted on FTP servers, while some of them also give the interface to display the list of files hosted on FTP servers.

### Other

LibreOffice declared its FTP support deprecated from the 7.4 release; this was later removed in the 24.2 release. Apache OpenOffice still supports FTP.

## Security

FTP was not designed to be a secure protocol, and has many security weaknesses. In May 1999, the authors of RFC 2577 listed a vulnerability to the following problems:

* Brute-force attack
* FTP bounce attack
* Packet capture
* Port stealing (guessing the next open port and usurping a legitimate connection)
* Spoofing attack
* Username enumeration
* DoS or DDoS

FTP does not encrypt its traffic; all transmissions are in clear text, and usernames, passwords, commands and data can be read by anyone able to perform packet capture (sniffing) on the network. Common solutions to this problem include:

* Using secure versions of insecure protocols (e.g., FTPS instead of FTP).
* Using a different, more secure protocol that can handle the job (e.g., SSH File Transfer Protocol or Secure Copy Protocol).
* Using a secure tunnel such as Secure Shell (SSH) or virtual private network (VPN).

### FTP over SSH

FTP over SSH is the practice of tunneling a normal FTP session over a Secure Shell connection. Because FTP uses multiple TCP connections, it is particularly difficult to tunnel over SSH. With many SSH clients, attempting to set up a tunnel for the control channel will protect only that channel; when data is transferred, the FTP software at either end sets up new TCP connections (data channels) and thus has no confidentiality or integrity protection. Otherwise, it is necessary for the SSH client software to have specific knowledge of the FTP protocol, to monitor and rewrite FTP control channel messages, and autonomously open new packet forwardings for FTP data channels.

## Derivatives

### FTPS

Explicit FTPS is an extension to the FTP standard that allows clients to request FTP sessions to be encrypted by sending the "AUTH TLS" command. The server has the option of allowing or denying connections that do not request TLS. Implicit FTPS is an outdated standard for FTP that requires the use of an SSL or TLS connection on different ports than plain FTP.

### SSH File Transfer Protocol

The SSH file transfer protocol (SFTP) transfers files and has a similar command set for users, but uses the Secure Shell protocol (SSH) to transfer files. Unlike FTP, it encrypts both commands and data, preventing passwords and sensitive information from being transmitted openly over the network. It cannot interoperate with FTP software.

### Trivial File Transfer Protocol

Trivial File Transfer Protocol (TFTP) is a simple, lock-step FTP that allows a client to get a file from or put a file onto a remote host. One of its primary uses is in the early stages of booting from a local area network, because TFTP is very simple to implement. TFTP lacks security and most of the advanced features offered by more robust file transfer protocols.

### Simple File Transfer Protocol

Simple File Transfer Protocol (the first protocol abbreviated SFTP), as defined by RFC 913, was proposed as an (unsecured) file transfer protocol with a level of complexity intermediate between TFTP and FTP. It was never widely accepted on the Internet and is now assigned Historic status by the IETF. It runs through port 115.

## FTP commands

The full list of FTP commands can be found in the relevant standard specifications.

## FTP reply codes

FTP reply codes are standardized in RFC 959 as three-digit values:

* **2yz:** Success reply
* **4yz or 5yz:** Failure reply
* **1yz or 3yz:** Error or Incomplete reply

The second digit defines the kind of error:

* **x0z:** Syntax errors.
* **x1z:** Replies for requests for information.
* **x2z:** Connections (control and data connections).
* **x3z:** Authentication and accounting (login process).
* **x5z:** File system status codes.
