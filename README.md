# Wireshark Network Traffic Analysis

For this project, I used Wireshark to capture and examine DNS, ICMP, TCP, and TLS traffic. I also compared a successful HTTPS connection with a failed TCP connection to practice identifying common network behavior and basic connection problems.

![TCP three-way handshake](screenshots/tcp-handshake.png)

## What I Did

- Used `nslookup` to generate DNS traffic for `example.com`
- Pinged `8.8.8.8` and reviewed the ICMP requests and replies
- Captured a successful TCP three-way handshake
- Examined a TLS 1.3 Client Hello and the encrypted traffic that followed
- Tested a failed TCP connection and identified repeated SYN retransmissions
- Redacted private network information before publishing the screenshots

## Tools

- Wireshark 4.6.8
- Windows Command Prompt and PowerShell
- `nslookup`
- `ping`
- `curl`
- `Test-NetConnection`

## Filters Used

```text
dns.qry.name == "example.com"
icmp
tls.handshake.extensions_server_name == "example.com"
ip.addr == 192.0.2.1 && tcp
```

## What I Found

The DNS capture showed A and AAAA lookups for `example.com`. My ICMP test captured four echo requests and four matching replies, confirming that the destination was reachable.

For the successful HTTPS connection, I found the SYN, SYN-ACK, and ACK packets that completed the TCP three-way handshake. The TLS handshake began afterward, and the application traffic was encrypted.

The failed connection showed a different pattern. The computer sent an initial SYN followed by four retransmissions, but no SYN-ACK came back. This could point to a blocked port, an unreachable host, a routing issue, or a service that is not responding.

A more detailed breakdown is available in [analysis/findings.md](analysis/findings.md).

## Screenshots

### DNS

![DNS query](screenshots/dns-query.png)

![DNS response](screenshots/dns-response.png)

### ICMP

![ICMP overview](screenshots/icmp-overview.png)

![ICMP echo request](screenshots/icmp-request.png)

![ICMP echo reply](screenshots/icmp-reply.png)

### Successful TCP and TLS Connection

![TCP three-way handshake](screenshots/tcp-handshake.png)

![TLS Client Hello](screenshots/tls-client-hello.png)

### Failed TCP Connection

![TCP SYN retransmissions](screenshots/tcp-failed-connection.png)

## What I Practiced

- Capturing and filtering network traffic
- Reading DNS, ICMP, TCP, and TLS packets
- Recognizing a TCP three-way handshake
- Troubleshooting a failed connection using packet evidence
- Writing clear technical notes
- Sanitizing information before sharing it publicly

## Privacy

I removed local addresses, hardware information, temporary client ports, and raw packet bytes from the screenshots. I did not upload the original packet captures because they may contain private network data.
