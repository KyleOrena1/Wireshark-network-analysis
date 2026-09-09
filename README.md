# Wireshark Network Traffic Analysis

This project shows how I used Wireshark to capture and analyze DNS, ICMP, TCP, and TLS traffic. I also compared a successful TCP connection with a failed one to demonstrate basic network troubleshooting.

## Project Goals

- Capture common network traffic in a controlled environment
- Use display filters to isolate specific protocols and connections
- Explain the TCP three-way handshake and TLS encryption
- Identify the packet pattern created by a failed connection
- Document the results without exposing private network information

## Tools

- Wireshark 4.6.8
- Windows Command Prompt and PowerShell
- `nslookup`
- `ping`
- `curl`
- `Test-NetConnection`

## Traffic Analyzed

- DNS A and AAAA queries for `example.com`
- ICMP echo requests and replies to `8.8.8.8`
- A successful TCP three-way handshake
- A TLS 1.3 Client Hello and encrypted application traffic
- A failed TCP connection with repeated SYN packets

## Wireshark Filters

```text
dns.qry.name == "example.com"
icmp
tls.handshake.extensions_server_name == "example.com"
ip.addr == 192.0.2.1 && tcp
```

## Results

The successful connection completed the SYN, SYN-ACK, ACK handshake before starting TLS. The failed connection sent an initial SYN and four retransmissions but never received a SYN-ACK.

This comparison shows how Wireshark can distinguish a working connection from a destination that may be blocked or unreachable.

Read the full [analysis](analysis/findings.md).

## Skills Demonstrated

- Packet capture and traffic filtering
- DNS, ICMP, TCP, and TLS analysis
- TCP connection troubleshooting
- Technical documentation
- Data sanitization and privacy awareness

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

## Privacy

I redacted local addresses, hardware information, temporary client ports, and raw packet bytes before publishing the screenshots. The original packet captures remain private.
