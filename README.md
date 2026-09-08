# Wireshark Network Traffic Analysis

## Project Overview

This project documents a controlled Wireshark capture used to examine DNS resolution, ICMP connectivity testing, the TCP three-way handshake, and TLS-encrypted HTTPS traffic.

## What I Analyzed

- DNS A and AAAA queries for `example.com`
- ICMP echo requests and replies involving `8.8.8.8`
- TCP SYN, SYN-ACK, and ACK packets
- A TLS 1.3 Client Hello and encrypted application traffic

## Tools

- Wireshark 4.6.8
- Windows Command Prompt
- `nslookup`
- `ping`
- `curl`

## Display Filters

```text
dns.qry.name == "example.com"
icmp
tcp.port == <temporary-client-port>
tls.handshake.extensions_server_name == "example.com"
```

## Results

The capture demonstrated how DNS resolves domain names, how ICMP tests reachability, how TCP establishes a connection, and how TLS protects HTTPS application data.

Read the full [analysis findings](analysis/findings.md).

## Evidence

### DNS Query and Response

![Redacted DNS query](screenshots/dns-query.png)

![Redacted DNS response](screenshots/dns-response.png)

### ICMP Connectivity Test

![Redacted ICMP overview](screenshots/icmp-overview.png)

![Redacted ICMP echo request](screenshots/icmp-request.png)

![Redacted ICMP echo reply](screenshots/icmp-reply.png)

### TCP and TLS

![Redacted TCP three-way handshake](screenshots/tcp-handshake.png)

![Redacted TLS Client Hello](screenshots/tls-client-hello.png)

## Repository Structure

- `analysis/` — written findings
- `screenshots/` — redacted packet evidence
- `captures/` — packet captures kept private unless reviewed and sanitized

## Privacy

Private client addresses, hardware addresses, temporary client ports, raw packet bytes, and potentially sensitive packet data were redacted or excluded. Traffic was captured only on an authorized device and network.
