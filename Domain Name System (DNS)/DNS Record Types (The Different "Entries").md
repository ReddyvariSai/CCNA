# DNS Record Types: The Complete Guide

## **DNS Records = Database Entries**
Think of DNS as a **distributed database**, and records are the **rows/entries** in that database. Each record type serves a specific purpose in directing internet traffic.

---

## **Core DNS Record Types (The Essentials)**

### **1. A Record (Address Record)**
**Purpose:** Maps a domain name to an IPv4 address
**Analogy:** Your home's street address (IPv4 version)
```
example.com  →  93.184.216.34
```
**TTL Example:** `3600` (seconds to cache = 1 hour)
**Use Case:** Hosting websites, pointing domains to servers

### **2. AAAA Record (Quad-A Record)**
**Purpose:** Maps a domain name to an IPv6 address
**Analogy:** Your home's street address (IPv6 version)
```
example.com  →  2606:2800:220:1:248:1893:25c8:1946
```
**Note:** "AAAA" because IPv6 addresses are 4× longer than IPv4 (32-bit → 128-bit)

### **3. CNAME Record (Canonical Name)**
**Purpose:** Creates an alias pointing to another domain name
**Analogy:** Nickname or forwarding address
```
www.example.com  →  example.com
shop.example.com  →  stores.shopify.com
```
**CRITICAL RULES:**
- Cannot have other records for same name (no A + CNAME together)
- Cannot point to another CNAME (technically can, but bad practice)
- Commonly used for: `www` subdomain, CDN setups

### **4. MX Record (Mail Exchange)**
**Purpose:** Directs email to mail servers
**Analogy:** Postal office address for your domain
```
example.com  MX  10  mail1.example.com
example.com  MX  20  mail2.example.com
```
**Key Fields:**
- **Priority:** Lower number = higher priority (try 10 first, then 20)
- **Server:** Domain name of mail server (must have A/AAAA record)
**Example Email Flow:**
1. Email to `user@example.com`
2. Sender asks: "MX for example.com?"
3. DNS: "Send to `mail1.example.com` (priority 10)"
4. If `mail1` fails, try `mail2` (priority 20)

### **5. TXT Record (Text Record)**
**Purpose:** Store text information (verification, security, policies)
**Analogy:** Notices pinned on a bulletin board
```
# Email security (SPF)
"v=spf1 include:_spf.google.com ~all"

# Domain verification (Google)
"google-site-verification=abc123..."

# Email security (DKIM)
"v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC..."

# DMARC policy
"v=DMARC1; p=reject; rua=mailto:dmarc@example.com"
```
**Common Uses:**
- **SPF:** Prevent email spoofing (lists authorized senders)
- **DKIM:** Email signing/authentication
- **DMARC:** Email policy enforcement
- **Domain ownership verification** (Google, Microsoft, etc.)
- **SSL certificate validation** (Let's Encrypt)

### **6. NS Record (Name Server)**
**Purpose:** Specifies authoritative DNS servers for a domain
**Analogy:** "For questions about this domain, ask these servers"
```
example.com  NS  ns1.cloudflare.com
example.com  NS  ns2.cloudflare.com
```
**Where Found:**
- At your domain registrar (who delegates authority)
- In every DNS zone (points to servers hosting your DNS)

### **7. SOA Record (Start of Authority)**
**Purpose:** Administrative information about a DNS zone
**Analogy:** "Header information" for the zone file
```
example.com.  SOA  ns1.example.com.  admin.example.com.  (
    2024010101  ; Serial number (YYYYMMDDNN)
    3600        ; Refresh (1 hour)
    600         ; Retry (10 minutes)
    86400       ; Expire (1 day)
    300         ; Minimum TTL (5 minutes)
)
```
**Critical Components:**
- **Serial:** Must increment when zone changes (triggers zone transfers)
- **Refresh:** How often secondary servers check for updates
- **Retry:** If refresh fails, retry after this time
- **Expire:** If no contact with primary, stop serving after this time
- **TTL:** Default cache time for negative responses

---

## **Specialized DNS Record Types**

### **8. PTR Record (Pointer Record)**
**Purpose:** Reverse DNS lookup (IP → Domain)
**Analogy:** Reverse phone directory lookup
```
34.216.184.93.in-addr.arpa  →  example.com
```
**Format:** IP address reversed + `.in-addr.arpa` (IPv4) or `.ip6.arpa` (IPv6)
**Critical For:**
- Email server reputation (spam filters check reverse DNS)
- Network troubleshooting
- Security logging

### **9. SRV Record (Service Record)**
**Purpose:** Defines location of specific services
**Analogy:** "Here's where to find specific services"
```
# SIP VoIP servers
_sip._tcp.example.com  SRV  10 60 5060 sipserver.example.com

# XMPP/Jabber
_xmpp-client._tcp.example.com  SRV  5 0 5222 xmpp.example.com
```
**Format:** `_service._proto.name TTL class SRV priority weight port target`
- **Priority/Weight:** Load balancing
- **Port:** Which port to connect to
- **Target:** Server hostname

### **10. CAA Record (Certification Authority Authorization)**
**Purpose:** Specifies which CAs can issue SSL certificates for domain
**Analogy:** "Only these agencies can issue passports for this domain"
```
example.com  CAA  0 issue "letsencrypt.org"
example.com  CAA  0 issuewild "digicert.com"
example.com  CAA  0 iodef "mailto:security@example.com"
```
**Flags:** `0` (non-critical) or `128` (critical)
**Tags:** `issue`, `issuewild`, `iodef`

### **11. DNAME Record (Delegation Name)**
**Purpose:** Creates alias for entire subtree of domain
**Analogy:** "Redirect all subdomains"
```
old-example.com  DNAME  new-example.com
```
- `blog.old-example.com` → `blog.new-example.com`
- `shop.old-example.com` → `shop.new-example.com`
**Unlike CNAME:** Works for entire domain + subdomains

### **12. SSHFP Record (SSH Fingerprint)**
**Purpose:** Publishes SSH host key fingerprints
**Analogy:** "Here's my SSH server's digital fingerprint for verification"
```
example.com  SSHFP  1 1 123456789abcdef...
```
**Prevents SSH warnings:** "Host key verification failed"

---

## **Less Common but Useful Records**

### **13. TLSA Record (TLS Authentication)**
**Purpose:** Associates TLS server certificate with domain (DANE)
**Use:** Ensures TLS certificate is valid even if CA is compromised

### **14. DS Record (Delegation Signer)**
**Purpose:** Used in DNSSEC to create chain of trust
**Links child zone's key to parent zone**

### **15. NAPTR Record (Naming Authority Pointer)**
**Purpose:** Complex rewrite rules (used in VoIP, ENUM)
**Example:** Telephone number → SIP URI mapping

---

## **Practical Record Examples**

### **Complete Website Setup**
```
# Domain root
example.com      A       93.184.216.34
example.com      AAAA    2606:2800:220:1:248:1893:25c8:1946

# Common aliases
www.example.com  CNAME   example.com

# Email setup
example.com      MX      10 mail.example.com
mail.example.com A       93.184.216.35

# Email security
example.com      TXT     "v=spf1 mx ~all"
_dmarc.example.com TXT   "v=DMARC1; p=none; rua=mailto:dmarc@example.com"

# Services
example.com      TXT     "google-site-verification=abc123"
```

### **Cloud/SaaS Integration**
```
# Using Shopify
shop.example.com  CNAME   shops.myshopify.com

# Using AWS/CloudFront
cdn.example.com   CNAME   d123.cloudfront.net

# Using Google Workspace
@                 MX      1 aspmx.l.google.com
@                 MX      5 alt1.aspmx.l.google.com
@                 TXT     "v=spf1 include:_spf.google.com ~all"
```

---

## **Record Priority & Conflict Resolution**

### **Order of Evaluation**
1. **Exact match** (specific record wins)
2. **Wildcards** (`*.example.com` matches `blog.example.com`)
3. **CNAME restrictions** (no other records with same name)

### **Common Pitfalls**
```dns
# ❌ WRONG: CNAME with other records
blog.example.com  CNAME   other.com
blog.example.com  TXT     "some text"  # ERROR!

# ✅ CORRECT: Use A record instead
blog.example.com  A       192.0.2.1
blog.example.com  TXT     "some text"

# ❌ WRONG: MX pointing to CNAME
example.com  MX  10  mail.example.com
mail.example.com  CNAME  google.com  # Many mail servers reject this

# ✅ CORRECT: MX should point to A record
mail.example.com  A  192.0.2.2
```

---

## **TTL (Time To Live) - The Cache Timer**

**Every record has a TTL** = How long resolvers should cache it
- **Low TTL (300s = 5min):** Fast changes, failover scenarios
- **High TTL (86400s = 1 day):** Stable services, less DNS load

**Changing Records?**
1. Lower TTL **BEFORE** making changes (24-48 hours ahead)
2. Make the change
3. Wait for old TTL to expire (or increase TTL back)

---

## **DNS Record Tools & Commands**

```bash
# Query specific record types
dig A example.com
dig MX example.com +short
dig TXT example.com

# See ALL records for domain
dig ANY example.com  # Note: Many servers restrict ANY queries

# Trace delegation
dig NS example.com +trace

# Check reverse DNS
dig -x 93.184.216.34

# Online tools: mxtoolbox.com, dnschecker.org, digwebinterface.com
```

## **Quick Reference Table**

| Record | Purpose | Example | Notes |
|--------|---------|---------|-------|
| **A** | IPv4 address | `example.com → 1.2.3.4` | Most common |
| **AAAA** | IPv6 address | `example.com → 2001:db8::1` | IPv6 equivalent |
| **CNAME** | Alias | `www → example.com` | No other records |
| **MX** | Mail server | `@ → 10 mail.example.com` | Priority matters |
| **TXT** | Text data | SPF, verification | Multiple allowed |
| **NS** | Name server | `@ → ns1.example.com` | Delegation |
| **SOA** | Zone admin | Serial, refresh, expire | Zone header |
| **PTR** | Reverse lookup | `4.3.2.1.in-addr.arpa → example.com` | IP → Domain |
| **SRV** | Service location | VoIP, XMPP | Protocol/port |
| **CAA** | CA authorization | `0 issue "letsencrypt.org"` | SSL security |

DNS records are like the **configuration files of the internet**—each type serves a specific purpose in directing traffic, securing services, and enabling functionality. Understanding them is key to managing any online presence!
