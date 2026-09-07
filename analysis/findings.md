# Analysis Findings

## Environment

- Operating system: Windows
- Capture interface: Wi-Fi
- Analysis tool: Wireshark 4.6.8
- Traffic-generation tools: `nslookup`, `ping`, and `curl`
- Test destinations: `example.com` and `8.8.8.8`

Private client addresses, hardware addresses, and temporary client ports are omitted from this public report.

## DNS Analysis

I ran `nslookup example.com` and filtered the capture with:

```text
dns.qry.name == "example.com"
```

The computer sent DNS A and AAAA queries to the configured resolver on destination port 53. The A-record response returned `172.66.147.243` and `104.20.23.154`. The capture also returned IPv6 addresses through the AAAA query. The selected response arrived in approximately `19.71 ms`.

This demonstrates how DNS translates a domain name into IP addresses before a client connects to a server.

## ICMP Analysis

I ran `ping 8.8.8.8` and applied:

```text
icmp
```

The capture contained four echo requests and four echo replies.

- Destination: `8.8.8.8`
- Request type: `8` (Echo request)
- Reply type: `0` (Echo reply)
- Code: `0`
- Packet length: `74 bytes`
- ICMP data: `32 bytes`
- Request TTL: `128`
- Reply TTL: `115`
- First measured response time: `29.95 ms`

The identifier and sequence numbers matched each request to its reply. This confirmed that the destination was reachable and responding.

## TCP Analysis

I used `curl https://example.com` to create a controlled HTTPS connection. After locating the correct stream through the TLS Server Name Indication, I isolated that connection and observed:

1. Client to server: `SYN`
2. Server to client: `SYN, ACK`
3. Client to server: `ACK`

The server used TCP port `443`. The three-way handshake established a reliable connection before encrypted application data was exchanged.

## TLS Analysis

After the TCP handshake, the client sent a TLS 1.3 Client Hello. The Server Name Indication identified `example.com`. The server returned a Server Hello, and the connection continued with encrypted application data.

Useful filter:

```text
tls.handshake.extensions_server_name == "example.com"
```

Wireshark could display connection metadata such as protocols, server port, packet sizes, and timing, but it could not display the encrypted HTTPS application content.

## Key Findings

- DNS resolved a domain into IPv4 and IPv6 addresses.
- ICMP echo traffic confirmed network reachability.
- TCP used a three-way handshake before transferring data.
- HTTPS used TCP port 443 and TLS 1.3.
- TLS protected application content while leaving limited connection metadata visible.
