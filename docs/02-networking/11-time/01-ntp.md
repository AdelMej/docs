# Network Time Protocol

The Network Time Protocol (NTP) is a networking protocol for clock synchronization between computer systems over packet-switched, variable-latency data networks. In operation since before 1985, NTP is one of the oldest Internet protocols in current use. NTP was designed by David L. Mills of the University of Delaware.

NTP is intended to synchronize participating computers to within a few milliseconds of Coordinated Universal Time (UTC). It uses the intersection algorithm, a modified version of Marzullo's algorithm, to select accurate time servers and is designed to mitigate the effects of variable network latency. NTP can usually maintain time to within tens of milliseconds over the public Internet, and can achieve better than one millisecond accuracy in local area networks under ideal conditions. Asymmetric routes and network congestion can cause errors of 100 ms or more.

The protocol is usually described in terms of a client–server model, but can as easily be used in peer-to-peer relationships where both peers consider the other to be a potential time source. Implementations send and receive timestamps using the User Datagram Protocol (UDP); the service is normally on port number 123, and in some modes both sides use this port number. They can also use broadcasting or multicasting, where clients passively listen to time updates after an initial round-trip calibrating exchange. NTP supplies a warning of any impending leap second adjustment, but no information about local time zones or daylight saving time is transmitted.

The current protocol is version 4 (NTPv4), which is backward compatible with version 3.

## Clock synchronization algorithm

A typical NTP client regularly polls one or more NTP servers. The client must compute its time offset and round-trip delay. Time offset $\theta$ is the positive or negative difference in absolute time between the two clocks. It is defined by:

$$\theta = \frac{(t_{1}-t_{0})+(t_{2}-t_{3})}{2}$$

And the round-trip delay $\delta$ by:

$$\delta = (t_{3}-t_{0})-(t_{2}-t_{1})$$

where:
* $t_0$ is the client's timestamp of the request packet transmission,
* $t_1$ is the server's timestamp of the request packet reception,
* $t_2$ is the server's timestamp of the response packet transmission and,
* $t_3$ is the client's timestamp of the response packet reception.

To derive the expression for the offset, note that for the request packet:
$$t_{0} + \theta + \delta / 2 = t_{1}$$
and for the response packet:
$$t_{3} + \theta - \delta / 2 = t_{2}$$

Solving for $\theta$ yields the definition of the time offset.

The values for $\theta$ and $\delta$ are passed through filters and subjected to statistical analysis ("mitigation"). Outliers are discarded and an estimate of time offset is derived from the best three remaining candidates. The clock frequency is then adjusted to reduce the offset gradually ("discipline"), creating a feedback loop.

Accurate synchronization is achieved when both the incoming and outgoing routes between the client and the server have symmetrical nominal delay. If the routes do not have a common nominal delay, a systematic bias exists of half the difference between the forward and backward travel times. A number of approaches have been proposed to measure asymmetry, but among practical implementations only chrony seems to have one included.

## History

NTP was designed by David L. Mills. The technology was later described in the 1981 Internet Engineering Note (IEN) 173 and a public protocol was developed from it that was documented in RFC 778. It was first deployed in a local area network as part of the Hello routing protocol and implemented in the Fuzzball router.

In 1985, NTP version 0 (NTPv0) was implemented in both Fuzzball and Unix, and the packet header and calculations were documented in RFC 958. In 1988, a more complete specification of the NTPv1 protocol was published in RFC 1059. In 1989, RFC 1119 defined NTPv2, introducing a management protocol and cryptographic authentication scheme.

In 1992, RFC 1305 defined NTPv3, which included an analysis of all sources of error and introduced broadcast mode. In 2010, RFC 5905 was published containing a proposed specification for NTPv4. The reference implementation is currently maintained as an open source project led by Harlan Stenn.

## SNTP

While the full NTP protocol provides complex algorithms to filter network jitter and analyze clock drift, some use cases found the full protocol too complicated. In 1992, Simple Network Time Protocol (SNTP) was defined to fill this niche. The SNTPv3 standard describes a way to use NTPv3 such that no storage of state over an extended period is needed.

SNTP is fully interoperable with NTP since it utilizes the same packet format and port. However, client/server will lack the complex algorithms required to filter network jitter, analyze clock drift, or cross-reference multiple time sources. This makes it suitable for IoT devices and basic hardware that require "good enough" time without the overhead of a full NTP application stack.

## Clock strata

NTP uses a hierarchical, semi-layered system of time sources. Each level is termed a stratum:

*   **Stratum 0:** High-precision timekeeping devices such as atomic clocks, GNSS (including GPS), or other radio clocks. They are known as reference clocks.
*   **Stratum 1:** Computers whose system time is synchronized to within a few microseconds of their attached stratum 0 devices. These are primary time servers.
*   **Stratum 2:** Computers synchronized over a network to stratum 1 servers.
*   **Stratum 3:** Computers synchronized to stratum 2 servers.

The upper limit for stratum is 15; stratum 16 is used to indicate that a device is unsynchronized.

### Common time reference identifiers (refid) codes

| Refid | Clock Source |
| :--- | :--- |
| GOES | Geostationary Operational Environmental Satellite |
| GPS | Global Positioning System |
| GAL | Galileo Positioning System |
| PPS | Generic pulse-per-second |
| IRIG | Inter-Range Instrumentation Group |
| WWVB | LF Radio WWVB Fort Collins, Colorado 60 kHz |
| DCF/PZF | LF Radio DCF77 Mainflingen, DE 77.5 kHz |
| HBG | LF Radio HBG Prangins, HB 75 kHz |
| MSF | LF Radio MSF Anthorn, UK 60 kHz |
| JJY | LF Radio JJY Fukushima, JP 40 kHz, Saga, JP 60 kHz |
| LORC | MF Radio Loran-C station, 100 kHz |
| TDF | MF Radio Allouis, FR 162 kHz |
| CHU | HF Radio CHU Ottawa, Ontario |
| WWV | HF Radio WWV Fort Collins, Colorado |
| WWVH | HF Radio WWVH Kauai, Hawaii |
| NIST | NIST telephone modem |
| ACTS | NIST telephone modem |
| USNO | USNO telephone modem |
| PTB | German PTB time standard telephone modem |
| MRS | Multi Reference Sources (Informal) |
| GOOG | Google Refid used by Google NTP servers |

## Software implementations

*   **Reference implementation:** Ported to almost every computing platform. Runs as a daemon called `ntpd` under Unix or as a service under Windows.
*   **Windows Time (W32Time):** Included in all Microsoft Windows versions since Windows 2000.
*   **OpenNTPD:** An NTPv3/SNTPv4 implementation with a focus on security and privilege separation.
*   **NTPsec:** A fork of the reference implementation that has been systematically security-hardened.
*   **chrony:** An independent NTP implementation sponsored by Red Hat. It is designed for virtual machines and unstable environments.
*   **ntpd-rs:** A security-focused implementation written in the Rust programming language to offer memory safety guarantees.

## Leap seconds

On the day of a leap second event, `ntpd` receives notification from a configuration file, attached reference clock, or remote server. Some implementations use "leap smearing," which introduces the leap second incrementally during a period of 24 hours. This is used by Google, Amazon AWS, and Facebook.

## Security concerns

Because adjusting system time is a privileged operation, NTP code must run with some privileges. Potential issues include:
*   **Stack buffer overflows:** A vulnerability was discovered and patched in 2014.
*   **Man-in-the-middle attacks:** Servers can be susceptible unless packets are cryptographically signed.
*   **NTP amplification attacks:** Attackers can use NTP servers to participate in distributed denial of service (DDoS) attacks by spoofing return IP addresses.

## Secure extensions

*   **Autokey:** An older public key system in NTPv4 that is now considered impractical and should no longer be used.
*   **Network Time Security (NTS):** A secure version of NTPv4 with TLS and AEAD. It uses a separate "key establishment" server to handle heavy asymmetric cryptography.
*   **MS-SNTP:** A Microsoft approach to authenticate NTPv3/SNTPv4 packets using a Windows domain identity.

## NTP packet header format

| Offset | Field | Description |
| :--- | :--- | :--- |
| 0 | LI | Leap Indicator (2 bits) |
| | VN | Version Number (3 bits) |
| | Mode | Association mode (3 bits) |
| | Stratum | Distance from reference clock (8 bits) |
| | Poll | Max interval between messages (8 bits) |
| | Precision | System clock precision (8 bits) |
| 4 | Root Delay | Total round-trip delay to reference clock (32 bits) |
| 8 | Root Dispersion | Total dispersion to reference clock (32 bits) |
| 12 | Reference ID | Identifies specific server or reference clock (32 bits) |
| 16 | Reference Timestamp | Time clock was last set/corrected (64 bits) |
| 20 | Origin Timestamp | Time at client when request departed (64 bits) |
| 24 | Receive Timestamp | Local time when latest message arrived (64 bits) |
| 28 | Transmit Timestamp | Time at server when response left (64 bits) |

## Timestamps

The 64-bit binary fixed-point timestamps consist of a 32-bit part for seconds and a 32-bit part for fractional second. The time scale rolls over every 232 seconds (136 years) and uses an epoch of January 1, 1900. The first rollover occurs on February 7, 2036.

## NTP and Timezone configuration via DHCP

NTP distributes absolute time (UTC) but does not distribute local time zone information. These are handled via DHCP options:

### DHCPv4 Time Zone Options
*   **Option 100:** POSIX time zone string.
*   **Option 101:** IANA time zone database (tzdb) name.

### DHCPv6 Time Zone Options
*   **Option 41:** POSIX time zone string.
*   **Option 42:** Time zone database name.
