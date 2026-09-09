# Wireshark Analysis

## Setup

I captured traffic from my Windows laptop over Wi-Fi using Wireshark 4.6.8. I used `nslookup`, `ping`, `curl`, and `Test-NetConnection` to create traffic I could analyze.

I removed my local addresses, hardware information, and temporary ports before publishing the screenshots.

## DNS

I ran:

```text
nslookup example.com
```

Then I used this filter:

```text
dns.qry.name == "example.com"
```

The capture showed both A and AAAA queries. The A response returned `172.66.147.243` and `104.20.23.154`. The response took about `19.71 ms` and used DNS port `53`.

This showed me what happens before a browser connects to a website: the computer first asks a DNS server for the site's IP address.

## ICMP

I ran `ping 8.8.8.8` and filtered for:

```text
icmp
```

Wireshark captured four echo requests and four matching replies.

- Echo request: Type `8`, Code `0`
- Echo reply: Type `0`, Code `0`
- Packet size: `74 bytes`
- ICMP data: `32 bytes`
- First response time: `29.95 ms`

The matching identifiers and sequence numbers connected each request to its reply. Since every request received a reply, the destination was reachable.

## Successful TCP Connection

I ran `curl https://example.com` and found the correct connection by filtering for the site's TLS server name.

The first three packets were:

1. `SYN`
2. `SYN, ACK`
3. `ACK`

That completed the TCP three-way handshake. Afterward, the client and server began the TLS exchange over port `443`.

## TLS

The capture showed a TLS 1.3 Client Hello for `example.com`, followed by a Server Hello and encrypted application data.

I used:

```text
tls.handshake.extensions_server_name == "example.com"
```

Wireshark could still show the server name, protocol, packet sizes, and timing, but it could not display the encrypted page content. This is a useful example of the difference between encrypted content and visible connection metadata.

## Failed TCP Connection

To compare a successful connection with a failed one, I ran:

```powershell
Test-NetConnection 192.0.2.1 -Port 443
```

I filtered the capture with:

```text
ip.addr == 192.0.2.1 && tcp
```

The client sent an initial SYN and then retransmitted it four times. No SYN-ACK came back, so the handshake never completed.

A result like this can point to an unreachable system, a blocked port, a firewall rule, or a routing problem. It is different from the successful connection to `example.com`, where the SYN received a SYN-ACK immediately.

| Test | Packets observed | Result |
| --- | --- | --- |
| Successful HTTPS connection | SYN, SYN-ACK, ACK | Handshake completed |
| Failed connection | SYN followed by four retransmissions | No connection established |

## What I Learned

- DNS resolves names before most connections begin.
- ICMP can confirm whether a destination is reachable.
- A successful TCP connection starts with a three-way handshake.
- Repeated SYN packets without a SYN-ACK are a useful troubleshooting clue.
- TLS encrypts the application data, but some connection metadata remains visible.
