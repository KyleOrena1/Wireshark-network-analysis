# Wireshark Network Traffic Analysis

A hands-on packet analysis project examining DNS, ICMP, TCP, and TLS traffic in Wireshark. The lab compares a successful HTTPS connection with a failed TCP connection to show how packet captures support basic network troubleshooting.

![TCP three-way handshake](screenshots/tcp-handshake.png)

## Project Summary

| Area | What I analyzed | Key result |
| --- | --- | --- |
| DNS | A and AAAA queries for `example.com` | Confirmed name resolution before the HTTPS connection |
| ICMP | Echo requests and replies to `8.8.8.8` | Matched four requests with four successful replies |
| TCP | A successful three-way handshake | Identified the SYN, SYN-ACK, and ACK sequence |
| TLS | A TLS 1.3 Client Hello and encrypted traffic | Verified HTTPS setup while application data remained encrypted |
| Troubleshooting | A failed connection to a documentation-only test address | Observed repeated SYN retransmissions with no SYN-ACK |

## Tools Used

- Wireshark 4.6.8
- Windows Command Prompt and PowerShell
- `nslookup`
- `ping`
- `curl`
- `Test-NetConnection`

## Method

1. Generated controlled DNS, ICMP, TCP, and TLS traffic.
2. Captured the traffic through the active Wi-Fi interface.
3. Used Wireshark display filters to isolate each exchange.
4. Reviewed protocol fields, packet timing, and connection behavior.
5. Compared a completed TCP handshake with an unsuccessful connection attempt.
6. Redacted private network information before publishing screenshots.

## Display Filters

```text
dns.qry.name == "example.com"
icmp
tls.handshake.extensions_server_name == "example.com"
ip.addr == 192.0.2.1 && tcp
```

## Key Findings

- DNS resolution returned A and AAAA records before the HTTPS connection began.
- Four ICMP echo requests received four matching replies, confirming reachability.
- The successful HTTPS connection completed the TCP three-way handshake before beginning TLS.
- The failed connection produced an initial SYN and four retransmissions without a SYN-ACK.
- Repeated unanswered SYN packets can point to filtering, routing, reachability, or service availability problems.

Read the detailed packet-by-packet write-up in [analysis/findings.md](analysis/findings.md).

## Evidence

### DNS Resolution

![DNS query](screenshots/dns-query.png)

![DNS response](screenshots/dns-response.png)

### ICMP Reachability

![ICMP overview](screenshots/icmp-overview.png)

![ICMP echo request](screenshots/icmp-request.png)

![ICMP echo reply](screenshots/icmp-reply.png)

### Successful TCP and TLS Connection

![TCP three-way handshake](screenshots/tcp-handshake.png)

![TLS Client Hello](screenshots/tls-client-hello.png)

### Failed TCP Connection

![TCP SYN retransmissions](screenshots/tcp-failed-connection.png)

## Skills Demonstrated

- Packet capture and display filtering
- DNS, ICMP, TCP, and TLS analysis
- TCP connection troubleshooting
- Evidence-based technical documentation
- Data sanitization and privacy awareness

## Privacy Note

Local addresses, hardware information, temporary client ports, and raw packet bytes were removed from the published screenshots. Original packet captures are not included because they may contain sensitive network data.
