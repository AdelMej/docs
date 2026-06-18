# HTTP/3

HTTP/3 is the third major version of the Hypertext Transfer Protocol used to exchange information on the World Wide Web, complementing the widely deployed HTTP/1.1 and HTTP/2. Unlike previous versions which relied on the well-established TCP (published in 1974), HTTP/3 uses QUIC (officially introduced in 2021), a multiplexed transport protocol built on UDP.

HTTP/3 uses similar semantics compared to earlier revisions of the protocol, including the same request methods, status codes, and message fields, but encodes them and maintains session state differently. However, partially due to the protocol's adoption of QUIC, HTTP/3 has lower latency and loads more quickly in real-world usage when compared with previous versions: in some cases over four times faster than with HTTP/1.1 (which, for many websites, is the only HTTP version deployed).

As of September 2024, HTTP/3 is supported by more than 95% of major web browsers in use and 34% of the top 10 million websites. It has been supported by Chromium (and derived projects including Google Chrome, Microsoft Edge, Samsung Internet, and Opera) since April 2020 and by Mozilla Firefox since May 2021. Safari has implemented the standard since Sept 2024 (16+ supports HTTP/3 for all users, previously it was limited to a subset of users).

## History

HTTP/3 originates from an Internet Draft adopted by the QUIC working group. The original proposal was named "HTTP/2 Semantics Using The QUIC Transport Protocol," and later renamed "Hypertext Transfer Protocol (HTTP) over QUIC".

On 28 October 2018, Mark Nottingham, Chair of the IETF HTTP and QUIC Working Groups, proposed renaming HTTP-over-QUIC to HTTP/3 to clearly identify it as another binding of HTTP semantics to the wire protocol so people understand its separation from QUIC. Nottingham's proposal was accepted by fellow IETF members a few days later. The HTTP working group was chartered to assist the QUIC working group during the design of HTTP/3, then assume responsibility for maintenance after publication.

Support for HTTP/3 was added to Chrome (Canary build) in September 2019 and then eventually reached stable builds, but was disabled by a feature flag. It was enabled by default in April 2020. Firefox added support for HTTP/3 in November 2019 through a feature flag and started enabling it by default in April 2021 in Firefox 88. Experimental support for HTTP/3 was added to Safari Technology Preview on April 8, 2020, and was included with Safari 14 that ships with iOS 14 and macOS 11. Starting September 2024, HTTP/3 is supported for all users on Safari 16 or newer.

On 6 June 2022, IETF published HTTP/3 as a Proposed Standard in RFC 9114.

## Comparison with HTTP/1.1 and HTTP/2

HTTP semantics are consistent across versions: the same request methods, status codes, and message fields are typically applicable to all versions. The differences are in the mapping of these semantics to underlying transports. Both HTTP/1.1 and HTTP/2 use TCP as their transport. HTTP/3 uses QUIC, a transport layer network protocol which uses user space congestion control over the User Datagram Protocol (UDP).

The switch to QUIC aims to fix a major problem of HTTP/2 called "head-of-line blocking": because the parallel nature of HTTP/2's multiplexing is not visible to TCP's loss recovery mechanisms, a lost or reordered packet causes all active transactions to experience a stall regardless of whether that transaction was impacted by the lost packet. Because QUIC provides native multiplexing, lost packets only impact the streams where data has been lost.

The HTTPS DNS resource record as defined in RFC 9460 allows for connecting without first receiving the Alt-Svc header via previous HTTP versions, therefore removing the 1 RTT of handshaking of TCP. There is client support for HTTPS resource records since Firefox 92, iOS 14, reported Safari 14 support, and Chromium supports it behind a flag.

## Implementations

### Client

Browser support for HTTP/3:

* **Chrome**: Stable build (79) December 2019; Enabled by default April 2020.
* **Edge**: Stable build (79) December 2019; Enabled by default April 2020.
* **Firefox**: Stable build (72.0.1) January 2020; Enabled by default April 2021.
* **Safari**: Stable build (14.0) September 2020; Supported for all users on Safari 16 or newer as of September 2024.

### Libraries

Open-source libraries that implement client or server logic for QUIC and HTTP/3 include:

* **lsquic**: C (LiteSpeed)
* **nghttp3**: C
* **h2o**: C (Server only)
* **libcurl**: C (Client only)
* **MsQuic**: C (Microsoft)
* **proxygen**: C++ (Facebook)
* **Cronet**: C++ (Google)
* **.NET**: C# (using MsQuic)
* **quic-go**: Go
* **http3**: Haskell
* **Kwik**: Java
* **Flupke**: Java
* **aioquic**: Python
* **niquests**: Python (Client only)
* **quiche**: Rust (Cloudflare)
* **neqo**: Rust (Mozilla)
* **quinn**: Rust
* **s2n-quic**: Rust (Amazon Web Services)

### Server

* **LiteSpeed Web Server** (and OpenLiteSpeed) 6.0.2: First version to enable HTTP/3 by default (June 2021).
* **Caddy web server** v2.6.0: HTTP/3 enabled by default (September 2022).
* **Nginx**: Supports HTTP/3 since 1.25.0 (May 2023). Binary packages with support released in February 2023.
* **Microsoft IIS**: Enabled natively with Windows Server 2022/Windows 11.
* **HAProxy**: Supports HTTP/3 over QUIC since version 2.6 (May 2022).
* **Nimble Streamer**: Supports HTTP/3 since 4.1.8-1 for HTTP-based protocols.
