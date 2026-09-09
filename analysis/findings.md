# Detailed Wireshark Analysis

## Lab Setup

I completed the capture on a Windows laptop connected through Wi-Fi. I used Wireshark 4.6.8 along with Command Prompt and PowerShell. The test traffic was generated with `nslookup`, `ping`, `curl`, and `Test-NetConnection`.

Before uploading the screenshots, I removed local addresses, hardware information, temporary client ports, and raw packet bytes.

## DNS

I started by running:

```text
nslookup example.com
```

I then isolated the DNS traffic with this filter:

```text
dns.qry.name == "example.com"
```

The capture included both A and AAAA queries. The A response returned `172.66.147.243` and `104.20.23.154`. The response used DNS port `53` and arrived in about `19.71 ms`.

This showed the computer resolving the domain name before connecting to the website.

## ICMP

Next, I ran `ping 8.8.8.8` and used the `icmp` filter. Wireshark captured four echo requests and four replies.

- Echo request: Type `8`, Code `0`
- Echo reply: Type `0`, Code `0`
- Packet size: `74 bytes`
- ICMP payload: `32 bytes`
- First response time: `29.95 ms`

The identifiers and sequence numbers connected each request to its reply. Since all four replies came back, the destination was reachable during the test.

## Successful TCP Connection

To create an HTTPS connection, I ran:

```text
curl https://example.com
```

I located the connection by filtering for the site's TLS server name. The first three packets in the connection were:

1. `SYN`
2. `SYN, ACK`
3. `ACK`

These packets completed the TCP three-way handshake. The client and server then started a TLS session over port `443`.

## TLS

The capture showed a TLS 1.3 Client Hello for `example.com`, followed by a Server Hello and encrypted application traffic.

I used this filter:

```text
tls.handshake.extensions_server_name == "example.com"
```

Wireshark still showed connection details such as the server name, protocol, packet sizes, and timing, but the HTTPS page content was encrypted.

## Failed TCP Connection

For comparison, I tested a connection to `192.0.2.1`, an address reserved for documentation and testing:

```powershell
Test-NetConnection 192.0.2.1 -Port 443
```

I filtered the capture with:

```text
ip.addr == 192.0.2.1 && tcp
```

The computer sent an initial SYN and retransmitted it four times. No SYN-ACK came back, so the TCP handshake never finished.

That pattern does not prove one specific cause by itself. It can appear when a host is unreachable, a firewall is dropping traffic, a route is unavailable, or a service is not responding.

| Connection | Packets observed | Result |
| --- | --- | --- |
| Successful HTTPS connection | SYN, SYN-ACK, ACK | Handshake completed |
| Failed connection | Initial SYN and four retransmissions | No connection established |

## Takeaways

This project helped me connect the packet details in Wireshark to what was happening on the network. I practiced following DNS resolution, matching ICMP requests with replies, identifying a complete TCP handshake, and recognizing the pattern of an unanswered connection attempt. I also saw how TLS protects web content while leaving some connection information visible.
