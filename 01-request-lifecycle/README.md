# 🌐 The Request-Response Lifecycle & Core Networking

## 🏢 Architectural Overview
A comprehensive breakdown of the low-level network mechanics, transport protocols, and gateway routing layers executed when a client initializes an HTTP request to an enterprise application.

## 🧱 Core Engineering Components

### 1. DNS Resolution (Domain Name System)
- **Mechanic:** Translates human-readable hostnames into machine-routable IPv4/IPv6 addresses via a hierarchical distributed database topology.
- **Production Trade-off:** High TTLs minimize global DNS resolution latency but delay routing propagation during server IP migration/failover strategies.

### 2. Transport Layer Security (TCP/IP & TLS Handshakes)
- **Reliability (TCP):** Establishes a connection via a deterministic 3-Way Handshake (`SYN` -> `SYN-ACK` -> `ACK`), ensuring zero packet loss and ordered delivery.
- **Encryption (TLS 1.3):** Executes a single round-trip cryptographic handshake to exchange ephemeral symmetric keys, preventing eavesdropping and tampering on sensitive data channels.

### 3. Edge Routing & Reverse Proxies (Nginx / Cloudflare)
- **Defensive Line:** Acts as an intermediary proxy gateway between public internet traffic and the internal application cluster network.
- **Core Functions:**
  - **SSL/TLS Termination:** Offloads resource-intensive decryption tasks from the application runtime thread.
  - **Header Sanitization:** Enforces secure CORS, CSP, and XSS headers before routing requests inward.

---

## 📊 Complete System Topology

sequenceDiagram
    autonumber
    actor User as Client Browser
    participant DNS as DNS Root Server
    participant Proxy as Reverse Proxy (Nginx)
    participant App as Express Backend API

    Note over User, DNS: Step 1: DNS Resolution
    User->>+DNS: Resolve URL to IP (Where is jurisynth.com?)
    DNS-->>-User: Return Target IP Address (192.0.2.1)

    Note over User, Proxy: Step 2 & 3: Connection & Security
    User->>+Proxy: TCP 3-Way Handshake (SYN)
    Proxy-->>User: SYN-ACK
    User->>Proxy: ACK (TCP Connection Established)
    User->>Proxy: TLS Cryptographic Handshake (Exchange Keys)
    Proxy-->>-User: Secure HTTPS Pipe Active

    Note over Proxy, App: Step 4: Reverse Proxy Routing
    User->>+Proxy: GET /api/cases (Encrypted Payload)
    Note over Proxy: Decrypts SSL Payload<br/>Filters Malicious Traffic
    Proxy->>+App: Forward Clean HTTP Request (Internal Network)
    App->>App: Execute Route Middleware & Controller
    App-->>-Proxy: 200 OK (JSON Payload Response)
    Note over Proxy: Encrypts Payload Data
    Proxy-->>-User: Stream Secured Response to Client