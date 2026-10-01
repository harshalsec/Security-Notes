# Day 2 — Networking Fundamentals

## 1. TCP vs UDP

### TCP

TCP (Transmission Control Protocol) is a connection-oriented transport-layer protocol used for reliable and ordered communication between network endpoints.

Before normal TCP communication begins, TCP performs a **3-way handshake**:

1. The client sends **SYN** to the server.
2. The server responds with **SYN-ACK**.
3. The client sends **ACK** back to the server.

```text
Client → Server: SYN
"Hi, can we communicate?"

Server → Client: SYN-ACK
"Yes, I received you and I am ready."

Client → Server: ACK
"Great, confirmed. Let's communicate."
```

After the handshake, the connection can be used for data transfer.

TCP provides mechanisms for reliable and ordered delivery. If data is lost, TCP can detect the problem and retransmit data as necessary.

### UDP

UDP (User Datagram Protocol) is a connectionless transport-layer protocol.

Unlike TCP, UDP does **not** perform the TCP 3-way handshake before sending data. It has lower overhead and does not provide TCP's built-in guarantees of reliable, ordered delivery.

A simple way to remember UDP is:

```text
"Here is the data. Send it."
```

UDP itself does not guarantee that data will arrive, arrive only once, or arrive in the correct order. An application using UDP can implement its own reliability mechanisms if it needs them.

### Easy memory

```text
TCP = Connection + Reliability + Order
UDP = Connectionless + Lower overhead
```

---

## 2. TCP 3-Way Handshake

The three steps are:

1. **SYN** — Synchronize
2. **SYN-ACK** — Synchronize + Acknowledge
3. **ACK** — Acknowledge

```text
Client                  Server

   SYN  ────────────────→
        ←──────────────── SYN-ACK
   ACK  ────────────────→

       Connection established
```

### What if the third step never happens?

If the final ACK does not arrive, the TCP handshake is not completed.

This **does not automatically mean an attack**. Possible reasons include packet loss, firewall filtering, network problems, the destination becoming unavailable, an abandoned connection attempt, or network scanning.

However, if a system receives a **large number of incomplete connection attempts**, especially across many different ports, that pattern can be a sign of network scanning or another suspicious activity.

A SOC analyst should investigate the **pattern and context**, rather than assuming that one incomplete handshake is malicious.

---

## 3. IP Address vs Port

### IP Address

An IP (Internet Protocol) address is a network-layer address used to identify a network endpoint and help route traffic between networks.

A useful analogy is:

> **IP address = building address**

For example:

```text
192.168.1.10
```

An IPv4 address contains **four octets**, and each octet can have a value from **0 to 255**.

IPv6 uses a different format and provides a much larger address space.

### Important correction

It is not correct to say that every device simply has "two IP addresses: a personal IP and a public IP."

A device may have a private IPv4 address, a public IPv4 address associated with its internet connection, an IPv6 address, or multiple addresses/interfaces depending on its network configuration.

### Port

A port identifies a **service endpoint** associated with network communication on a host.

A useful analogy is:

> **IP address = building address**
>
> **Port = particular door/service in that building**

For example:

```text
192.168.1.10:443
```

means traffic is being directed to port **443** on the host at `192.168.1.10`.

### Common ports to remember

| Port | Common service/protocol | Easy memory |
|---:|---|---|
| 21 | FTP | File Transfer |
| 22 | SSH | Secure remote administration |
| 23 | Telnet | Remote terminal |
| 25 | SMTP | Sending email |
| 53 | DNS | Domain name resolution |
| 80 | HTTP | Web |
| 110 | POP3 | Receiving email |
| 143 | IMAP | Receiving/synchronizing email |
| 443 | HTTPS | Secure web |
| 3306 | MySQL | Database |
| 3389 | RDP | Windows Remote Desktop |

### Important correction about ICMP

**ICMP does not use TCP/UDP port numbers.**

So `143` is not an ICMP port. Port `143` is commonly associated with **IMAP**.

The key distinction is:

```text
TCP/UDP → have ports
ICMP    → does not use TCP/UDP ports
```

---

## 4. Why Can DNS Be Abused?

DNS stands for **Domain Name System**.

Its normal job is to translate domain names into IP addresses.

```text
google.com
    ↓
DNS resolution
    ↓
IP address
```

DNS is essential to normal network communication, so organizations commonly allow DNS traffic.

Attackers can attempt to abuse DNS as a **covert communication channel**. One example is **DNS tunneling**, where information is encoded into DNS queries and responses to communicate with attacker-controlled infrastructure.

A SOC analyst may investigate DNS traffic that is unusually frequent, unusually long, random-looking, repeatedly sent to a suspicious domain, or very different from the normal DNS behavior of that device.

### Important point

Unusual DNS traffic does **not automatically prove an attack**. Some legitimate applications can also generate unusual-looking DNS requests.

The correct SOC mindset is:

> **Unusual DNS behavior → investigate the context and pattern.**

---

## 5. My `nslookup google.com` Result

### Output I received

My `nslookup google.com` command returned information similar to:

```text
Server:  UnKnown
Address: 10.15.63.206
```

It then returned multiple IPv6 and IPv4 addresses for `google.com`, including:

```text
2404:6800:4000:101d::71
2404:6800:4000:101d::64
2404:6800:4000:101d::8b
2404:6800:4000:101d::66
```

and IPv4 addresses such as:

```text
142.251.126.101
142.251.126.102
142.251.126.139
142.251.126.100
142.251.126.138
142.251.126.113
```

### What happened?

When I ran:

```bash
nslookup google.com
```

my computer asked its configured DNS resolver for the IP addresses associated with `google.com`.

The line:

```text
Address: 10.15.63.206
```

is the address of the DNS server/resolver that `nslookup` used.

The fact that it displayed:

```text
Server: UnKnown
```

does not mean that DNS failed. It generally means that `nslookup` could not determine a hostname for the DNS server through reverse DNS.

The results beginning with:

```text
2404:...
```

are **IPv6 addresses**.

The results beginning with:

```text
142.251...
```

are **IPv4 addresses**.

Google can have multiple IP addresses for the same domain because large internet services use multiple servers, networks, and locations for availability, load distribution, and performance.

### Simple mental model

```text
My computer
     |
     | "What IP addresses belong to google.com?"
     ↓
DNS resolver: 10.15.63.206
     |
     | "Here are several IPv4/IPv6 addresses."
     ↓
My computer
```

---

# Key Takeaways

## 1. OSI model

For SOC work, the most important simplified mental model is:

```text
Layer 7 → WHAT?
           Application protocols such as HTTP, DNS, FTP

Layer 4 → HOW?
           TCP, UDP, and ports

Layer 3 → WHO?
           IP addresses and routing

Layer 2 → WHICH LOCAL DEVICE?
           MAC addresses and ARP

Layer 1 → HOW DOES THE SIGNAL TRAVEL?
           Cables, radio/Wi-Fi, and physical transmission
```

I do not need to memorize every OSI layer in detail yet. I mainly need to understand what these important layers represent.

## 2. TCP vs UDP

TCP is a connection-oriented transport protocol that uses a 3-way handshake:

```text
SYN → SYN-ACK → ACK
```

TCP provides reliable and ordered communication mechanisms.

UDP is connectionless, does not use the TCP 3-way handshake, and has lower overhead. UDP itself does not guarantee reliable or ordered delivery.

## 3. IP address vs port

An **IP address** identifies a network endpoint and helps traffic reach the correct destination.

A **port** identifies a service endpoint on that host.

Think:

```text
IP   = Building address
Port = Door/service
```

Important ports:

```text
21   → FTP
22   → SSH
23   → Telnet
53   → DNS
80   → HTTP
110  → POP3
143  → IMAP
443  → HTTPS
3306 → MySQL
3389 → RDP
```

ICMP does not use TCP/UDP port numbers.

## 4. DNS

DNS stands for **Domain Name System**.

Its main job is to resolve domain names into IP addresses.

```text
google.com
     ↓
DNS
     ↓
IP address
```

Attackers can abuse DNS for covert communication, including DNS tunneling.

A SOC analyst should pay attention to unusual DNS patterns, but unusual DNS traffic alone does not prove malicious activity.

## 5. SOC mindset

When I see a network event, I should ask:

### WHO?

```text
Source IP
Destination IP
```

### HOW?

```text
TCP or UDP
Port
```

### WHAT?

```text
Application protocol
Traffic/request details
```

### IS IT NORMAL?

```text
Is this expected for this device and user?
Is the destination normal?
Is the volume unusual?
Is the timing unusual?
Is the pattern suspicious?
```

This is the beginning of network-based SOC investigation.

---

# Mistakes I Made and What I Learned

## Mistake 1 — Saying UDP can be requested again by UDP itself

I wrote that if UDP data is not received completely, I can request it again.

**Correction:** UDP itself does not provide retransmission or reliability. An application using UDP can implement its own retry/retransmission mechanism if required.

## Mistake 2 — Saying every device has a private IP and a public IP

This is an oversimplification.

A device can have multiple network addresses depending on its interfaces and configuration. Private IPv4, public IPv4, and IPv6 are different concepts.

## Mistake 3 — Confusing port numbers

The important corrections are:

```text
21   → FTP
22   → SSH
23   → Telnet
53   → DNS
80   → HTTP
110  → POP3
143  → IMAP
443  → HTTPS
3306 → MySQL
3389 → RDP
```

## Mistake 4 — Saying ICMP uses port 143

ICMP does **not** use TCP/UDP ports.

Port `143` is commonly used by **IMAP**.

```text
TCP/UDP → ports
ICMP    → no TCP/UDP ports
```

## Mistake 5 — Saying DNS tunneling "interrupts" communication

DNS tunneling is better understood as **abusing DNS as a communication channel**.

The attacker may encode information in DNS queries/responses to communicate with attacker-controlled infrastructure.

## Mistake 6 — Treating an incomplete TCP handshake as automatically malicious

An incomplete handshake can happen for many legitimate reasons.

A SOC analyst looks for **patterns and context**.

```text
One incomplete connection
        ↓
Could be normal

Thousands of incomplete connections
to many different ports
        ↓
Worth investigating for possible scanning
```

---

# Final Memory Map

```text
                    NETWORK TRAFFIC
                          |
              +-----------+-----------+
              |                       |
             WHO?                    HOW?
              |                       |
          IP Address              TCP / UDP
              |                       |
              |                     PORT
              |                       |
              +-----------+-----------+
                          |
                         WHAT?
                          |
                    Application
                 HTTP / DNS / FTP
                          |
                          v
                    SOC ANALYSIS
                          |
                  "Is this normal?"
```

## The 5 things I should remember from Day 2

1. **L3 = IP = WHO**
2. **L4 = TCP/UDP + ports = HOW**
3. **L7 = Application protocols = WHAT**
4. **TCP handshake = SYN → SYN-ACK → ACK**
5. **DNS = domain name → IP address**
