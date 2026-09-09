# Wireshark Analysis

## Purpose

The goal of this project was to capture common network traffic, analyze how the protocols behave, and use packet evidence to compare a successful TCP connection with a failed one.

## Lab Setup

- Windows laptop connected through Wi-Fi
- Wireshark 4.6.8
- Command Prompt and PowerShell
- Traffic generated with `nslookup`, `ping`, `curl`, and `Test-NetConnection`

Local addresses, hardware information, temporary client ports, and raw packet bytes were removed from the published screenshots.

## DNS Analysis

I ran:

```text
nslookup example.com
```

I isolated the traffic with:

```text
dns.qry.name == "example.com"
```

The capture showed A and AAAA queries. The A response returned `172.66.147.243` and `104.20.23.154`. It used DNS port `53` and arrived in about `19.71 ms`.

This confirmed that the client resolved the domain name before connecting to the website.

## ICMP Analysis

I ran `ping 8.8.8.8` and applied the `icmp` filter. Wireshark captured four echo requests and four matching replies.

- Echo request: Type `8`, Code `0`
- Echo reply: Type `0`, Code `0`
- Packet size: `74 bytes`
- ICMP payload: `32 bytes`
- First response time: `29.95 ms`

The identifiers and sequence numbers matched each request with its reply. All four replies were received, confirming that the destination was reachable.

## Successful TCP Connection

I ran `curl https://example.com` and located the correct connection by filtering for the site's TLS server name.

The connection began with:

1. `SYN`
2. `SYN, ACK`
3. `ACK`

These packets completed the TCP three-way handshake. The client and server then began a TLS session over port `443`.

## TLS Analysis

The capture showed a TLS 1.3 Client Hello for `example.com`, followed by a Server Hello and encrypted application data.

I used:

```text
tls.handshake.extensions_server_name == "example.com"
```

Wireshark displayed connection metadata such as the server name, protocol, packet sizes, and timing. The HTTPS page content remained encrypted.

## Failed TCP Connection

To compare the successful connection with a failed one, I ran:

```powershell
Test-NetConnection 192.0.2.1 -Port 443
```

I isolated the packets with:

```text
ip.addr == 192.0.2.1 && tcp
```

The client sent an initial SYN and retransmitted it four times. No SYN-ACK returned, so the handshake never completed.

This pattern can indicate an unreachable system, a blocked port, a firewall rule, or a routing problem.

| Test | Packets observed | Result |
| --- | --- | --- |
| Successful HTTPS connection | SYN, SYN-ACK, ACK | Handshake completed |
| Failed connection | SYN followed by four retransmissions | No connection established |

## Key Takeaways

- DNS resolves domain names before most connections begin.
- ICMP can confirm whether a destination is reachable.
- TCP uses a three-way handshake to establish a connection.
- Repeated SYN packets without a SYN-ACK provide a clear troubleshooting clue.
- TLS protects application content while leaving limited connection metadata visible.
