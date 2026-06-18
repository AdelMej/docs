# HTTPS

Hypertext Transfer Protocol Secure (HTTPS) is an extension of the Hypertext Transfer Protocol (HTTP). It uses encryption for secure communication over a computer network and is widely used on the Internet. In HTTPS, the communication protocol is encrypted using Transport Layer Security (TLS) or, formerly, Secure Sockets Layer (SSL). The protocol is therefore also referred to as HTTP over TLS or HTTP over SSL.

The principal motivations for HTTPS are the authentication of the accessed website and the protection of the privacy and integrity of the exchanged data while it is in transit. It protects against man-in-the-middle attacks, and the bidirectional block cipher encryption of communications between a client and server protects the communications against eavesdropping and tampering. The authentication aspect of HTTPS requires a trusted third party to sign server-side digital certificates.

In 2016, a campaign by the Electronic Frontier Foundation with the support of web browser developers led to the protocol becoming more prevalent. HTTPS has since 2018 been used more often by web users than non-secure HTTP, primarily to protect page authenticity on all types of websites, secure accounts, and keep user communications, identity, and web browsing private.

## Overview

The Uniform Resource Identifier (URI) scheme HTTPS has identical usage syntax to the HTTP scheme. However, HTTPS signals the browser to use an added encryption layer of SSL/TLS to protect the traffic. SSL/TLS is especially suited for HTTP since it can provide some protection even if only one side of the communication is authenticated, which is the case with most HTTP transactions over the Internet where typically only the server is authenticated.

HTTPS creates a secure channel over an insecure network. This ensures reasonable protection from eavesdroppers and man-in-the-middle attacks, provided that adequate cipher suites are used and that the server certificate is verified and trusted.

Because HTTPS piggybacks HTTP entirely on top of TLS, the entirety of the underlying HTTP protocol can be encrypted. This includes the request's URL, query parameters, headers, and cookies (which often contain identifying information about the user). However, because website addresses and port numbers are necessarily part of the underlying TCP/IP protocols, HTTPS cannot protect their disclosure. In practice, even on a correctly configured web server, eavesdroppers can infer the IP address and port number of the web server, and sometimes even the domain name (e.g., <www.example.org>, but not the rest of the URL) that a user is communicating with, along with the amount of data transferred and the duration of the communication, though not the content of the communication.

Web browsers know how to trust HTTPS websites based on certificate authorities that come pre-installed in their software. A user should trust an HTTPS connection to a website when:

* The user trusts that their device is not compromised.
* The user trusts that the browser software correctly implements HTTPS with correctly pre-installed certificate authorities.
* The user trusts the certificate authority to vouch only for legitimate websites.
* The website provides a valid certificate signed by a trusted authority.
* The certificate correctly identifies the website.
* The user trusts that the protocol's encryption layer (SSL/TLS) is sufficiently secure against eavesdroppers.

HTTPS is especially important over insecure networks, such as public Wi-Fi access points, which allow anyone on the same local network to packet-sniff and discover sensitive information not protected by HTTPS. It is also important for connections over the Tor network to prevent malicious Tor nodes from damaging or altering the contents passing through them.

As more information is revealed about global mass surveillance and criminals stealing personal information, the use of HTTPS security on all websites is becoming increasingly important regardless of the type of Internet connection being used. Even though metadata about individual pages might not be considered sensitive, when aggregated it can reveal a lot about the user and compromise privacy.

Deploying HTTPS also allows the use of HTTP/2 and HTTP/3 (and their predecessors SPDY and QUIC), which are new HTTP versions designed to reduce page load times, size, and latency. It is recommended to use HTTP Strict Transport Security (HSTS) with HTTPS to protect users from man-in-the-middle attacks, especially SSL stripping.

## Usage in Websites

As of April 2018, 33.2% of Alexa top 1,000,000 websites use HTTPS as default, and 70% of page loads (measured by Firefox Telemetry) use HTTPS. As of June 2025, 71.2% of the Internet's 150,000 most popular websites have a secure implementation of HTTPS. However, despite TLS 1.3's release in 2018, adoption has been slow, with many still remaining on the older TLS 1.2 protocol.

## Browser Integration

Most browsers display a warning if they receive an invalid certificate. Newer browsers display a warning across the entire window and prominently display the site's security information in the address bar. Extended validation certificates show the legal entity on the certificate information. Most browsers also display a warning to the user when visiting a site that contains a mixture of encrypted and unencrypted content.

The Electronic Frontier Foundation provided an add-on called HTTPS Everywhere for Mozilla Firefox, Google Chrome, Chromium, and Android, which enables HTTPS by default for hundreds of frequently used websites. Forcing a web browser to load only HTTPS content has been supported in Firefox starting in version 83. Starting in version 94, Google Chrome is able to "always use secure connections" if toggled in the browser's settings.

## Security

The security of HTTPS is that of the underlying TLS, which typically uses long-term public and private keys to generate a short-term session key used to encrypt the data flow between the client and the server. X.509 certificates are used to authenticate the server (and sometimes the client as well). Certificate authorities and public key certificates are necessary to verify the relation between the certificate and its owner.

An important property in this context is forward secrecy, which ensures that encrypted communications recorded in the past cannot be retrieved and decrypted should long-term secret keys or passwords be compromised in the future. Not all web servers provide forward secrecy.

For HTTPS to be effective, a site must be completely hosted over HTTPS. If some of the site's contents are loaded over HTTP (scripts or images, for example), or if only a certain page that contains sensitive information is loaded over HTTPS while the rest of the site is loaded over plain HTTP, the user will be vulnerable to attacks and surveillance. Additionally, cookies on a site served through HTTPS must have the secure attribute enabled.

## Technical

### Difference from HTTP

* HTTPS URLs begin with "https://" and use port 443 by default, whereas HTTP URLs begin with "http://" and use port 80 by default.
* HTTP is not encrypted and thus is vulnerable to man-in-the-middle and eavesdropping attacks. HTTPS is designed to withstand such attacks and is considered secure against them (with the exception of HTTPS implementations that use deprecated versions of SSL).

### Network Layers

HTTP operates at the highest layer of the TCP/IP model—the application layer; as does the TLS security protocol (operating as a lower sublayer of the same layer), which encrypts an HTTP message prior to transmission and decrypts a message upon arrival. Strictly speaking, HTTPS is not a separate protocol, but refers to the use of ordinary HTTP over an encrypted SSL/TLS connection.

HTTPS encrypts all message contents, including the HTTP headers and the request/response data. With the exception of certain cryptographic attacks, an attacker should at most be able to discover that a connection is taking place between two parties, along with their domain names and IP addresses.

### Server Setup

To prepare a web server to accept HTTPS connections, the administrator must create a public key certificate for the web server. This certificate must be signed by a trusted certificate authority for the web browser to accept it without warning.

#### Acquiring Certificates

A number of commercial certificate authorities exist, offering paid-for SSL/TLS certificates of a number of types, including Extended Validation Certificates. Let's Encrypt, launched in April 2016, provides free and automated service that delivers basic SSL/TLS certificates to websites. The majority of web hosts and cloud providers now leverage Let's Encrypt.

#### Use as Access Control

The system can also be used for client authentication in order to limit access to a web server to authorized users. To do this, the site administrator typically creates a certificate for each user, which the user loads into their browser. The certificate contains the name and e-mail address of the authorized user and is automatically checked by the server on each connection to verify the user's identity, potentially without even requiring a password.

#### In Case of Compromised Secret (Private) Key

An important property in this context is perfect forward secrecy (PFS). Possessing one of the long-term asymmetric secret keys used to establish an HTTPS session should not make it easier to derive the short-term session key to then decrypt the conversation, even at a later time. Diffie–Hellman key exchange (DHE) and Elliptic-curve Diffie–Hellman key exchange (ECDHE) are the only schemes known to have that property. TLS 1.3, published in August 2018, dropped support for ciphers without forward secrecy.

#### Certificate Revocation

A certificate may be revoked before it expires, for example because the secrecy of the private key has been compromised. Newer versions of popular browsers implement the Online Certificate Status Protocol (OCSP) to verify that this is not the case. The browser sends the certificate's serial number to the certificate authority or its delegate via OCSP and the authority responds, telling the browser whether the certificate is still valid or not. The CA may also issue a CRL to tell people that these certificates are revoked.

### Limitations

* SSL/TLS encryption can be configured in simple mode (authentication by server only) or mutual mode (requires user to install a personal client certificate).
* SSL/TLS does not prevent the indexing of the site by a web crawler, and in some cases the URI of the encrypted resource can be inferred by knowing only the intercepted request/response size.
* Because TLS operates at a protocol level below that of HTTP and has no knowledge of the higher-level protocols, TLS servers can only strictly present one certificate for a particular address and port combination. A solution called Server Name Indication (SNI) exists, which sends the hostname to the server before encrypting the connection.
* A sophisticated type of man-in-the-middle attack called SSL stripping defeats the security provided by HTTPS by changing the https: link into an http: link. This prompted the development of HTTP Strict Transport Security (HSTS).
* HTTPS has been shown to be vulnerable to a range of traffic analysis attacks, which are side-channel attacks that rely on variations in the timing and size of traffic to infer properties about the encrypted traffic itself.
* The fact that most modern websites use HTTPS causes problems for many users trying to access public Wi-Fi hot spots, because a captive portal Wi-Fi hot spot login page fails to load if the user tries to open an HTTPS resource. Several websites, such as NoSSL.sh, guarantee that they will always remain accessible by HTTP.

## History

Netscape Communications created HTTPS in 1994 for its Netscape Navigator web browser. Originally, HTTPS was used with the SSL protocol. As SSL evolved into Transport Layer Security (TLS), HTTPS was formally specified by RFC 2818 in May 2000. Google announced in February 2018 that its Chrome browser would mark HTTP sites as "Not Secure" after July 2018 to encourage website owners to implement HTTPS.
