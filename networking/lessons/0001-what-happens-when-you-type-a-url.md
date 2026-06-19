# Lesson 1: What Happens When You Type a URL

**Lesson 1** · ~20 min · Hands-on

> [!NOTE] Mission tie-in
> Every time you visit a website, dozens of networking concepts work together seamlessly. Understanding this flow is essential for debugging, system design, and security.

## What You'll Learn

- The full journey of a URL from browser to server and back
- DNS resolution, TCP handshake, HTTP request/response
- Use networking tools to observe each step

## The Journey in 6 Steps

```
You type "https://example.com"
        │
        ▼
1. DNS Lookup — "What IP is example.com?"
        │
        ▼
2. TCP Handshake — "Can we talk?" SYN → SYN-ACK → ACK
        │
        ▼
3. TLS Handshake — "Let's encrypt" (if HTTPS)
        │
        ▼
4. HTTP Request — "GET / HTTP/2"
        │
        ▼
5. Server Response — "200 OK" + HTML
        │
        ▼
6. Browser Renders — Parse HTML, fetch assets, display page
```

## Step 1: DNS Resolution

Your browser doesn't know where `example.com` is. It asks DNS:

```bash
# See it happen
nslookup example.com
# Server: 192.168.1.1 (your router)
# Address: 93.184.216.34  ← the IP

# Alternative
dig +short example.com
```

DNS is a distributed database. Your query goes:
1. Browser cache → OS cache → Router cache → ISP DNS → Root → TLD → Authoritative

> [!TIP] `nslookup` is available on all platforms. It's your first debugging tool for "the website won't load."

## Step 2: TCP Three-Way Handshake

Before any data is sent, TCP establishes a connection:

```
Client                    Server
  │                         │
  │────── SYN ────────────► │  "Hey, can we talk?"
  │                         │
  │◄───── SYN-ACK ──────── │  "Sure, I'm ready"
  │                         │
  │────── ACK ────────────► │  "Great, let's go"
  │                         │
  │══════ Data Flow ══════► │  Actual HTTP request
```

```bash
# See the handshake (requires Wireshark or tcpdump)
# On Linux/Mac:
sudo tcpdump -c 3 host example.com
```

## Step 3: TLS Handshake (HTTPS)

For HTTPS, after TCP comes TLS — establishes encryption:

```
Client                          Server
  │                               │
  │────── ClientHello ──────────► │  "I support TLS 1.3, these ciphers..."
  │                               │
  │◄───── ServerHello ────────── │  "Let's use TLS 1.3, cipher X"
  │◄───── Certificate ────────── │  "Here's my SSL cert, verify me"
  │◄───── Key Exchange ───────── │  "Here's the key material"
  │                               │
  │────── Finished ────────────► │  "Ready to encrypt"
  │══════ Encrypted Data ═══════► │
```

```bash
# See TLS details with curl
curl -vI https://example.com 2>&1 | grep -i "SSL\|TLS\|handshake"
```

## Step 4: HTTP Request

```bash
# See the raw HTTP request and response
curl -v https://example.com

# Just the headers
curl -I https://example.com
```

The request:
```
GET / HTTP/2
Host: example.com
User-Agent: curl/...
Accept: */*
```

The response:
```
HTTP/2 200
content-type: text/html; charset=UTF-8
content-length: 1256
server: ECS (dcb/7F14)
```

## Step 5: Read the Response

```bash
# Download and see the HTML
curl -s https://example.com | head -20
```

The HTML includes links to CSS, JS, images — the browser makes additional requests for each.

## Observe It All with curl

```bash
# Verbose shows EVERY step
curl -v --trace-time https://example.com > /dev/null
```

Look for:
- `Trying 93.184.216.34:443...` (DNS resolution)
- `Connected to example.com` (TCP handshake complete)
- `SSL connection using TLSv1.3` (TLS established)
- `> GET / HTTP/2` (request sent)
- `< HTTP/2 200` (response received)

## Hands-On

```bash
# 1. Resolve a domain
nslookup google.com

# 2. Trace the network path
traceroute google.com

# 3. See the full HTTP conversation
curl -v https://example.com 2>&1 | less

# 4. Check response headers
curl -I https://github.com

# 5. Follow a redirect
curl -vL http://github.com 2>&1 | grep -i "location\|HTTP/"
```

## Self-Quiz

<details>
<summary><strong>1.</strong> What's the purpose of the TCP three-way handshake?</summary>

To establish a reliable connection before data is sent. SYN → SYN-ACK → ACK ensures both sides are ready to communicate.
</details>

<details>
<summary><strong>2.</strong> Why is DNS caching important?</summary>

Without caching, every request would go through the full DNS resolution chain (browser → OS → router → ISP → root → TLD → authoritative). Caching makes repeated lookups nearly instant.
</details>

<details>
<summary><strong>3.</strong> What's the difference between HTTP and HTTPS at the connection level?</summary>

HTTPS adds a TLS handshake AFTER the TCP handshake and BEFORE the HTTP data. This encrypts everything after the handshake. HTTP sends data in plaintext.
</details>

## Next Steps

- Run `curl -v` on your own sites and study the output
- Try `curl -vI` on a site that uses HTTP/2 vs HTTP/1.1
- Move to Lesson 2: TCP & UDP Deep Dive (coming next)

> [!NOTE] Ask followup questions!
> Not seeing a step in the output? Want to capture packets with Wireshark? Ask your teacher.

---

*Reference: [HTTP: The Definitive Guide](https://www.oreilly.com/library/view/http-the-definitive/1565925092/)*
