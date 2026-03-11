# Networking Fundamentals

## Table of Contents

1. [Foundational Concepts](#part-1-foundational-concepts)
2. [Network Infrastructure](#part-2-network-infrastructure)
3. [The OSI Model and TCP/IP Stack](#part-3-the-osi-model-and-tcpip-stack)
4. [How Data Travels](#part-4-how-data-travels---step-by-step)
5. [Advanced Concepts](#part-6-advanced-concepts)
6. [Security Concepts](#part-7-security-concepts)
7. [Practical Tools and Commands](#part-8-practical-tools-and-commands)
8. [Real-World Example](#part-9-real-world-example---complete-journey)

---

## Part 1: Foundational Concepts

### What is the Internet?

The Internet is a global network of interconnected computers and devices that communicate using standardized protocols. Think of it as a massive web of roads connecting billions of devices worldwide, allowing them to exchange information.

---

### IP Addresses (Internet Protocol Addresses)

An IP address is a unique numerical identifier assigned to every device connected to a network. It's like a postal address for your computer — it tells other devices where to send information.

#### IPv4 (Internet Protocol version 4)

- Format: Four numbers separated by periods (e.g., `192.168.1.1`)
- Each number ranges from 0 to 255
- Total possible addresses: ~4.3 billion
- Problem: We've nearly run out of IPv4 addresses

#### IPv6 (Internet Protocol version 6)

- Format: Eight groups of hexadecimal numbers (e.g., `2001:0db8:85a3:0000:0000:8a2e:0370:7334`)
- Created to solve the IPv4 shortage
- Provides approximately 340 undecillion addresses (340 with 36 zeros)

#### Types of IP Addresses

| Type | Description |
|------|-------------|
| **Public IP** | Visible to the internet, assigned by your ISP |
| **Private IP** | Used within your local network (e.g., `192.168.x.x`, `10.x.x.x`) |
| **Static IP** | Permanent address that doesn't change |
| **Dynamic IP** | Temporary address that changes periodically |

---

### MAC Addresses (Media Access Control Address)

A MAC address is a permanent, unique hardware identifier burned into every network interface card (NIC) during manufacturing. Unlike IP addresses, MAC addresses do not change.

- **Format:** Six pairs of hexadecimal digits (e.g., `00:1A:2B:3C:4D:5E`)

#### Key Differences from IP Addresses

| | MAC Address | IP Address |
|---|---|---|
| **Layer** | Layer 2 (Data Link) | Layer 3 (Network) |
| **Scope** | Local network communication | Routing across networks |
| **Analogy** | Apartment number | Full street address |

---

### Ports and Protocols

Ports are virtual endpoints for communication. A single device can run multiple services simultaneously using different ports.

#### Common Ports

| Port | Protocol | Purpose |
|------|----------|---------|
| 22 | SSH | Secure remote access |
| 25 | SMTP | Email sending |
| 53 | DNS | Domain name resolution |
| 80 | HTTP | Web traffic |
| 443 | HTTPS | Secure web traffic |
| 3389 | RDP | Remote Desktop |

#### Key Protocols

- **HTTP/HTTPS** — Web browsing
- **TCP** — Reliable, ordered delivery
- **UDP** — Fast, connectionless delivery
- **FTP** — File transfer
- **SMTP/POP3/IMAP** — Email
- **DNS** — Domain name resolution

---

### DNS (Domain Name System)

DNS is the "phonebook of the internet." It translates human-readable domain names (like `google.com`) into IP addresses that computers can understand.

#### How DNS Works

1. You type `example.com` in your browser
2. Your computer checks its local DNS cache
3. If not found, queries your configured DNS server (ISP's or public DNS like `8.8.8.8`)
4. The DNS server looks up the IP address for `example.com`
5. Returns the IP address (e.g., `93.184.216.34`)
6. Your computer connects to that IP address

#### DNS Hierarchy

- **Root servers** — Top-level DNS servers (13 worldwide)
- **TLD servers** — Manage top-level domains (`.com`, `.org`, `.uk`)
- **Authoritative servers** — Store the actual IP addresses for specific domains

#### DNS Record Types

| Record | Purpose |
|--------|---------|
| **A** | Maps domain to IPv4 address |
| **AAAA** | Maps domain to IPv6 address |
| **CNAME** | Creates an alias for another domain |
| **MX** | Specifies mail servers for the domain |
| **TXT** | Stores text information (often for verification) |

---

### Domains, Subdomains, and URLs

#### Domain Structure

A domain like `blog.shop.example.com` breaks down as:

```
blog   .   shop   .   example   .   com
 └─ sub-subdomain    └─ subdomain   └─ TLD
                  └─ second-level domain
```

#### URL Components

```
https://www.example.com:443/path/to/page?query=value#section
  │         │       │    │       │            │          │
protocol  subdomain domain port  path      query     fragment
```

---

### Subnets and Subnet Masks

A subnet divides a larger network into smaller, manageable sections.

- **Subnet Mask** — Determines which portion of an IP address is the network vs. the host
- Example: `255.255.255.0` means the first three octets identify the network
- **CIDR notation**: `192.168.1.0/24` — the `/24` means 24 bits for the network portion

---

## Part 2: Network Infrastructure

### Network Devices

| Device | Function |
|--------|----------|
| **Router** | Connects different networks; routes packets using IP addresses; performs NAT |
| **Switch** | Connects devices within the same network using MAC addresses (Layer 2) |
| **Modem** | Converts digital ↔ analog signals for cable/DSL/fiber transmission |
| **Gateway** | Entry/exit point between networks (often your router) |
| **Firewall** | Monitors and controls incoming/outgoing traffic |
| **Access Point** | Extends a wired network to wireless devices |

---

### ISP (Internet Service Provider)

Your ISP is the company that provides your internet connection. They:

- Assign you a public IP address
- Route your traffic to the broader internet
- Connect to other ISPs through peering agreements
- May cache popular content for faster access

---

## Part 3: The OSI Model and TCP/IP Stack

### The OSI Model (7 Layers)

| Layer | Name | What It Does | Examples |
|-------|------|-------------|---------|
| 7 | **Application** | User-facing layer | HTTP, HTTPS, FTP, SMTP, DNS |
| 6 | **Presentation** | Data formatting, encryption, compression | SSL/TLS, JPEG, GIF |
| 5 | **Session** | Manages connections between applications | Session establishment & termination |
| 4 | **Transport** | End-to-end communication and reliability | TCP, UDP |
| 3 | **Network** | Routing and forwarding packets | IP, ICMP |
| 2 | **Data Link** | Communication between devices on same network | Ethernet, Wi-Fi (802.11) |
| 1 | **Physical** | Actual physical transmission medium | Cables, radio waves, fiber optics |

### TCP/IP Model (4-Layer Simplified)

| TCP/IP Layer | Corresponds To (OSI) |
|---|---|
| Application | Layers 5–7 |
| Transport | Layer 4 |
| Internet | Layer 3 |
| Network Access | Layers 1–2 |

---

## Part 4: How Data Travels — Step by Step

### Scenario 1: Basic Web Request

**You type `www.example.com` and press Enter:**

#### Step 1 — DNS Resolution
1. Browser checks its cache for the IP address
2. If not cached, checks the OS DNS cache
3. If still not found, queries your router's DNS cache
4. Router forwards query to your ISP's DNS server
5. DNS server returns the IP address (e.g., `93.184.216.34`)

#### Step 2 — TCP Three-Way Handshake
1. Your computer sends a **SYN** packet to the server
2. Server responds with **SYN-ACK**
3. Your computer sends **ACK**
4. Connection established

#### Step 3 — HTTP Request Creation

```http
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0...
```

#### Step 4 — Packet Encapsulation

Each layer wraps the data with its own header:

```
[ Application ] → HTTP request data
[ Transport   ] → + TCP header (source/destination ports)
[ Network     ] → + IP header (source/destination IPs)
[ Data Link   ] → + Ethernet header (source/destination MACs)
[ Physical    ] → Converted to electrical/optical signals
```

#### Steps 5–10 — Routing and Response

1. Packet sent to router via Ethernet/Wi-Fi (ARP used to find router's MAC)
2. Router performs NAT, replaces private IP with public IP, forwards to ISP
3. Packet traverses 10–30+ routers using BGP routing tables (`traceroute` shows this)
4. Packet arrives at the destination web server, layers are de-encapsulated
5. Server processes request and sends HTTP response
6. Response travels back; router performs reverse NAT
7. Browser renders the HTML page

---

### Scenario 2: Local Network Communication

**You ping `192.168.1.100`:**

1. Your computer checks its subnet mask — destination is on the same network
2. Sends an ARP broadcast: *"Who has `192.168.1.100`?"*
3. That device replies with its MAC address
4. ICMP echo request sent directly — no router needed

---

### Scenario 3: With a VPN

1. VPN client establishes an encrypted tunnel to the VPN server
2. Your packet is encrypted and wrapped in a new packet addressed to the VPN server ("packet within a packet")
3. Your ISP sees: traffic to a VPN server IP, amount of data, timing — **but not** what sites you visit
4. VPN server decrypts, extracts your original packet, forwards it to the destination
5. Response returns via VPN server, re-encrypted, decrypted at your end

**Why use a VPN:**
- Privacy from ISP and local networks
- Bypass geographical restrictions
- Security on public Wi-Fi
- Hide real IP from websites

---

### Scenario 4: With a Firewall

#### Outbound Request Flow

```
Browser → Port 80 request
  → Firewall: Port 80 allowed? ✓ → App allowed? ✓ → Domain blocked? ✗
  → Packet forwarded
```

#### Inbound Response Flow

```
Response arrives
  → Firewall: Part of existing connection? ✓ → Matches expected response? ✓
  → Packet delivered to computer
```

#### Blocked Scenario

```
Unsolicited packet on port 445 arrives
  → Firewall: Active outbound connection on 445? ✗ → Port 445 allowed inbound? ✗
  → Packet dropped silently
```

#### Firewall Rule Actions

| Action | Behavior |
|--------|---------|
| **Allow** | Permit traffic |
| **Block/Deny** | Drop traffic silently |
| **Reject** | Drop traffic and notify sender |

---

### Scenario 5: With a Proxy Server

#### Forward Proxy (Client-Side)

- Browser sends requests to proxy → proxy fetches content → returns to you
- Website only sees the proxy's IP address
- Use cases: anonymity, content filtering, caching, bypassing restrictions

#### Reverse Proxy (Server-Side)

- Request goes to proxy → proxy forwards to actual web server(s)
- Used for: load balancing, caching, security
- Example: Nginx, Cloudflare

---

### Scenario 6: HTTPS / TLS Encryption

#### TLS Handshake

1. **Client Hello** — Browser sends supported encryption methods
2. **Server Hello** — Server picks an encryption method
3. **Certificate** — Server sends SSL/TLS certificate
4. **Verification** — Browser verifies with Certificate Authority (CA)
5. **Key Exchange** — Encryption keys established
6. **Encrypted Connection** — All subsequent data is encrypted

#### What observers can and cannot see

| Visible | Not Visible |
|---------|-------------|
| IP addresses | Specific pages visited |
| Domain name (SNI) | Form data / passwords |
| Amount of data | Content of responses |
| Timing | |

---

## Part 5: Complete Workflows

### Simple Network

```
[Your Computer]
    → (Ethernet/Wi-Fi)
[Home Router]
    → (Cable/DSL/Fiber)
[ISP Router]
    → (Internet Backbone)
[ISP Router (Destination)]
    → (Local Network)
[Web Server]
```

### Complex Network with Security

```
[Your Computer]
    ↓ Check DNS cache
[Local DNS Cache]           ← Cache hit? Use cached IP
    ↓ Cache miss
[Host Firewall]             ← Check outbound rules
    ↓ Allowed
[Create Packet]             ← Application → Transport → Network → Data Link
    ↓
[VPN Client]                ← Encrypt packet (if VPN enabled)
    ↓
[Router NAT]                ← Translate private IP → public IP
    ↓
[Router Firewall]           ← Check security rules
    ↓ Allowed
[ISP Network]
    ↓
[DNS Server]                ← Resolve domain to IP (if needed)
    ↓
[Internet Routing]          ← Multiple hops via BGP
    ↓
[Destination ISP]
    ↓
[Firewall / WAF]            ← Web Application Firewall
    ↓ Allowed
[Reverse Proxy / Load Balancer]
    ↓
[Internal Firewall]
    ↓ Allowed
[Web Server]                ← Process request, generate response
    ↓
[Reverse Path]              ← Response follows same path back
    ↓
[Your Browser]              ← Render webpage
```

---

## Part 6: Advanced Concepts

### NAT (Network Address Translation)

**Why NAT exists:** IPv4 address shortage; allows many devices to share one public IP; hides internal IPs.

**How it works:**

```
Device (192.168.1.100:5000)  →  Router replaces source with public IP (203.0.113.50:6000)
                                Router stores mapping in translation table
Response arrives at (203.0.113.50:6000)  →  Router maps back to (192.168.1.100:5000)
```

| Type | Description |
|------|-------------|
| **SNAT** | Source NAT — changes source address (outbound) |
| **DNAT** | Destination NAT — changes destination address (port forwarding) |
| **PAT** | Port Address Translation — most common, multiplexes via ports |

---

### CDN (Content Delivery Network)

A CDN is a network of distributed servers that cache content closer to users.

**Flow:**
1. You request `www.example.com/image.jpg`
2. DNS returns IP of nearest CDN edge server
3. Edge server checks cache → serves if hit, otherwise fetches from origin, caches, then serves

**Benefits:** Faster load times, reduced bandwidth costs, better availability, DDoS protection.

---

### DHCP (Dynamic Host Configuration Protocol)

Automatically assigns IP addresses to devices. The process follows **DORA**:

| Step | Action |
|------|--------|
| **Discover** | New device broadcasts "I need an IP address" |
| **Offer** | DHCP server offers an available IP |
| **Request** | Device requests that specific IP |
| **Acknowledge** | Server confirms and assigns the IP |

**Information provided:** IP address, subnet mask, default gateway, DNS servers, lease time.

---

### TCP vs UDP

| | TCP | UDP |
|---|---|---|
| **Connection** | Connection-oriented | Connectionless |
| **Reliability** | Guaranteed delivery & order | No guarantee |
| **Speed** | Slower (more overhead) | Faster |
| **Use cases** | Web browsing, email, file transfer | Streaming, VoIP, gaming, DNS |

---

### Latency, Throughput, and Bandwidth

| Term | Definition |
|------|------------|
| **Latency** | Time for data to travel from source to destination (measured in ms) |
| **Throughput** | Actual data transmitted per unit time (may be less than bandwidth) |
| **Bandwidth** | Maximum capacity of a connection (Mbps, Gbps) — width of the pipe |

---

### Internet Backbone

High-capacity fiber connections between major networks, operated by Tier 1 ISPs.

| Tier | Description |
|------|-------------|
| **Tier 1** | Can reach entire internet without paying transit (e.g., AT&T, Level 3) |
| **Tier 2** | Pays some transit, peers with some networks |
| **Tier 3** | Your local ISP — pays for all transit |

---

## Part 7: Security Concepts

### Common Attacks and Protections

| Attack | Description | Protection |
|--------|-------------|------------|
| **DDoS** | Overwhelming a server with traffic from multiple sources | Firewalls, rate limiting, CDNs |
| **Man-in-the-Middle** | Intercepting communication between two parties | HTTPS/TLS, certificate verification, VPN |
| **DNS Spoofing** | Providing false DNS responses to redirect users | DNSSEC, secure DNS servers |
| **ARP Spoofing** | Fake ARP messages to associate attacker MAC with victim IP | Static ARP entries, detection tools |
| **Port Scanning** | Probing ports to find vulnerabilities | Firewalls, close unnecessary ports, IDS/IPS |

---

### Encryption Basics

#### Symmetric Encryption
- Same key for encryption and decryption
- Fast; problem is key distribution
- Example: **AES**

#### Asymmetric Encryption
- Public key encrypts, private key decrypts
- Slower; used in HTTPS handshake
- Example: **RSA**

#### Hashing
- One-way function; same input always produces same output
- Cannot be reversed
- Used for: password storage, data integrity
- Examples: **SHA-256**, MD5 (deprecated)

---

## Part 8: Practical Tools and Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ping` | Tests connectivity; sends ICMP echo requests | `ping google.com` |
| `traceroute` / `tracert` | Shows each router hop to destination | `traceroute google.com` |
| `nslookup` / `dig` | DNS lookup; queries DNS servers for records | `nslookup google.com` |
| `ipconfig` / `ifconfig` | Shows IP address, subnet mask, gateway, MAC | `ipconfig /all` |
| `netstat` | Shows active connections and listening ports | `netstat -an` |
| `arp` | Shows ARP cache (IP → MAC mappings) | `arp -a` |
| `route` | Shows/modifies the routing table | `route -n` |
| `nc` (netcat) | Tests connectivity to specific ports | `nc -zv google.com 80` |
| `curl` / `wget` | Makes HTTP requests from the command line | `curl https://example.com` |
| `wireshark` | Packet capture and analysis (GUI) | — |

---

## Part 9: Real-World Example — Complete Journey

### You Send an Email to `friend@example.com`

#### Step 1 — Composing (Application Layer)
You write the email and click **Send**.

#### Step 2 — DNS Resolution for Mail Server
Client queries DNS for `example.com`'s **MX record** → receives `mail.example.com` (`198.51.100.50`).

#### Step 3 — SMTP Connection
Your client connects to your outbound mail server (e.g., `smtp.gmail.com`) on port **587**, authenticates, and establishes a TCP connection.

#### Step 4 — SMTP Protocol Exchange

```
HELO client.com
MAIL FROM: <you@gmail.com>
RCPT TO: <friend@example.com>
DATA
Subject: Hello
Message content here
.
QUIT
```

#### Step 5 — Routing to Recipient's Mail Server
Your mail server looks up `example.com`'s MX record, connects to `mail.example.com`, and delivers the email via SMTP.

#### Step 6 — Recipient Retrieval
Friend's email client connects to `mail.example.com` and retrieves the email via **POP3** (download) or **IMAP** (sync).

#### Along the Way
- Packets traverse multiple routers
- Firewalls inspect each connection
- TLS encrypts all SMTP communication (if enabled)
- Anti-spam filters and virus scanners process the message
- **SPF** and **DKIM** verify sender authenticity
