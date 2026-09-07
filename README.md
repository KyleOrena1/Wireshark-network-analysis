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

## Repository Structure

- `analysis/` — written findings
- `screenshots/` — sanitized packet evidence when added
- `captures/` — packet captures kept private unless reviewed and sanitized

## Privacy

Private client addresses, hardware addresses, temporary client ports, and potentially sensitive packet data are excluded from the public report. Traffic was captured only on an authorized device and network.
