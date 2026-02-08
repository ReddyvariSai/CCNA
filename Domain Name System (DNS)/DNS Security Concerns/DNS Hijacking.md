# DNS Hijacking: The Complete Guide

## **What is DNS Hijacking?**
DNS Hijacking is the **unauthorized redirection of DNS queries** to malicious servers. Unlike DNS spoofing (which poisons caches), hijacking **changes where DNS queries go** in the first place.

### **Simple Analogy:**
Imagine changing someone's phone contacts so when they call "Mom," it connects to **your phone** instead.

---

## **The 5 Types of DNS Hijacking**

### **1. Local Device Hijacking**
**Target:** Individual computers/devices  
**Method:** Malware changes DNS settings locally  
**Impact:** Single device affected

```bash
# Before hijacking (Windows):
ipconfig /all
DNS Servers . . . . . . . . . . . : 8.8.8.8

# After hijacking:
ipconfig /all
DNS Servers . . . . . . . . . . . : 185.228.168.168  # Malicious DNS
```

**How it happens:**
- Trojan/malware with admin privileges
- Exploiting vulnerabilities in DNS client software
- Social engineering ("Your DNS needs updating")

### **2. Router Hijacking**
**Target:** Home/office routers  
**Method:** Compromise router admin interface  
**Impact:** **All devices** on network affected

```bash
# Common router vulnerabilities:
# - Default admin passwords (admin/admin)
# - Unpatched firmware
# - UPnP exploits
# - CSRF attacks via malicious websites
```

**Attack Flow:**
1. Attacker scans for vulnerable routers
2. Guesses/default password → gains access
3. Changes DNS settings in router admin
4. All network traffic now uses malicious DNS

### **3. Man-in-the-Middle (MitM)**
**Target:** Network traffic  
**Method:** Intercept DNS queries on the wire  
**Impact:** Anyone on compromised network

**Techniques:**
- **ARP spoofing:** Pretend to be gateway
- **Evil twin:** Rogue Wi-Fi access point
- **Compromised switch:** Network hardware hacked

```
Normal:    Computer → Router → ISP DNS
Hijacked:  Computer → Attacker → Router → ISP DNS
                    (intercepts & modifies)
```

### **4. ISP-Level Hijacking**
**Target:** ISP's DNS infrastructure  
**Method:** Compromise ISP's DNS servers  
**Impact:** Thousands/millions of customers

**Motivations:**
- Government censorship (China, Iran, Turkey)
- Corporate surveillance (some ISPs)
- Criminal compromise (ransomware, data theft)

**Examples:**
- **Brazil (2016):** Major ISPs DNS hijacked → bank phishing
- **Turkey (2014):** Government ordered DNS redirection of Twitter/YouTube
- **Pakistan (2008):** Hijacked YouTube globally for 2 hours

### **5. Registrar Hijacking**
**Target:** Domain registration  
**Method:** Take control of domain at registrar  
**Impact:** **Complete control** of domain's DNS

**How it happens:**
1. Social engineering registrar support
2. Compromised email accounts (password reset)
3. Registrar security vulnerabilities
4. Insider threats at registrar

**Consequences:**
- Can change ALL DNS records (A, MX, NS, etc.)
- Can transfer domain to another registrar
- Can sell/ransom the domain

---

## **Real-World Attack Scenarios**

### **Scenario 1: Home Router Compromise**
```
Family uses home Wi-Fi normally
↓
Attacker exploits router vulnerability (CVE-2023-XXXX)
↓
Changes router DNS to: 94.158.24.253 (malicious)
↓
Next day, daughter visits bank.com
↓
Browser shows "https://bank.com" ✅
Actually at: "94.158.24.253" (phishing site) ❌
↓
Credentials stolen, accounts emptied
```

### **Scenario 2: Coffee Shop MitM**
```
You connect to "Free_Public_WiFi"
↓
Attacker runs: 
  airbase-ng -e "Free_Public_WiFi" -c 6 wlan0
↓
Your DNS queries go to attacker's laptop
↓
facebook.com → attacker's phishing page
gmail.com → credential harvester
amazon.com → fake login page
↓
All credentials compromised in 30 minutes
```

### **Scenario 3: Nation-State Censorship**
```
Iranian user tries to access twitter.com
↓
ISP DNS (government-controlled) returns:
  twitter.com → 10.10.34.1 (government server)
↓
User sees: "This website is blocked"
Actually: Government tracking attempt + displaying block page
```

---

## **Technical Deep Dive: How It Works**

### **DNS Configuration Locations (Attack Points)**

#### **Windows:**
```powershell
# Location 1: Network adapter settings
Get-DnsClientServerAddress

# Location 2: Registry
Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\"

# Location 3: Group Policy
# Can override local settings

# Location 4: hosts file
C:\Windows\System32\drivers\etc\hosts
# Example malicious entry:
# 94.158.24.253   bank.com
```

#### **Linux/macOS:**
```bash
# /etc/resolv.conf (traditional)
nameserver 8.8.8.8

# systemd-resolved (modern)
/etc/systemd/resolved.conf
# DNS=94.158.24.253  # Hijacked!

# NetworkManager
/etc/NetworkManager/NetworkManager.conf
```

#### **Routers:**
```bash
# Common router interfaces:
# 192.168.1.1  # Default gateway
# 192.168.0.1
# 10.0.0.1

# DNS settings usually in:
# - Basic Setup → Network Address Server Settings
# - Internet/LAN → DHCP Server → DNS
```

### **Malware Techniques**

#### **1. DNSChanger Malware (Famous Example)**
**Operation Ghost Click (2011):**
- Infected 4 million computers
- Changed DNS to rogue servers
- Earned $14 million in ad fraud
- FBI had to set up clean DNS for victims

**How it worked:**
```python
# Pseudocode of DNSChanger malware
def hijack_dns():
    if os == "Windows":
        # Modify registry
        set_registry("HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters",
                    "NameServer", "85.255.112.10,85.255.112.20")
        
        # Block security updates
        firewall_block("*.microsoft.com")
        firewall_block("*.symantec.com")
        
    elif os == "macOS":
        # Modify resolv.conf
        write_file("/etc/resolv.conf", "nameserver 85.255.112.10")
        
    # Also hijack router if possible
    try:
        router_login("admin", "admin")
        router_set_dns("85.255.112.10", "85.255.112.20")
    except:
        pass
```

#### **2. VPNFilter Malware (Router-Specific)**
**Targeted 500,000 routers globally:**
- Modified DNS settings on routers
- Stole website credentials
- Could brick devices remotely

### **Government/ISP-Level Hijacking**

#### **China's Great Firewall DNS Redirection:**
```python
# Simplified version of GFW DNS filtering
def gfw_dns_filter(query):
    if query.domain in blocked_domains:
        # Return fake IP (government server)
        return "110.110.110.110"
    elif query.is_suspicious:
        # TCP reset or timeout
        send_tcp_reset(query.source_ip)
        return None
    else:
        # Pass through to real DNS
        return forward_to_real_dns(query)
```

#### **Turkey's Twitter Block (2014):**
```
March 20, 2014: Turkish court orders Twitter block
↓
TTNET (major ISP) changes DNS for twitter.com
twitter.com → 192.168.1.1 (local IP, fails)
↓
Workaround: Users change DNS to 8.8.8.8
↓
Government orders blocking of 8.8.8.8
↓
Cat-and-mouse game continues...
```

---

## **Detection Methods**

### **1. DNS Consistency Checking**
```python
def check_dns_hijacking():
    # Query multiple independent DNS servers
    trusted_resolvers = [
        '8.8.8.8',      # Google
        '1.1.1.1',      # Cloudflare
        '9.9.9.9',      # Quad9
        '208.67.222.222' # OpenDNS
    ]
    
    test_domains = [
        'google.com',
        'facebook.com',
        'microsoft.com',
        'example.com'
    ]
    
    results = {}
    for resolver in trusted_resolvers:
        for domain in test_domains:
            ip = dns_query(resolver, domain)
            results[(resolver, domain)] = ip
    
    # Check for inconsistencies
    for domain in test_domains:
        ips = [results[(r, domain)] for r in trusted_resolvers]
        if len(set(ips)) > 1:
            alert(f"DNS hijacking suspected for {domain}")
```

### **2. DNSSEC Validation**
```bash
# Check if DNSSEC is working
# If validation fails but site loads, possible hijacking
dig +dnssec bank.com | grep "ad flag"
# Should show: "ad" flag present (authenticated data)

# If you get response without ad flag for DNSSEC-signed domain
# → Likely hijacking or MITM
```

### **3. SSL/TLS Certificate Checking**
```python
def detect_ssl_hijacking(url):
    import ssl, socket
    
    # Get certificate from connection
    context = ssl.create_default_context()
    with socket.create_connection((url, 443)) as sock:
        with context.wrap_socket(sock, server_hostname=url) as ssock:
            cert = ssock.getpeercert()
    
    # Check certificate properties
    expected_issuer = "Let's Encrypt"  # or other known CA
    if cert['issuer'] != expected_issuer:
        return True  # Possible hijacking
    
    # Check certificate chain
    # Hijacked sites often use self-signed or wrong certs
```

### **4. TTL Anomaly Detection**
```python
# Legitimate DNS usually has consistent TTLs
# Hijacked DNS often has unusual TTLs

normal_ttl = 300  # 5 minutes typical
hijacked_ttl = 86400  # 1 day (attackers want persistence)

if dns_response.ttl > 3600:  # > 1 hour
    suspicious_counter += 1
```

### **5. Geographic Anomalies**
```python
# bank.com should resolve to US IPs
# If suddenly resolving to Russia/China → suspicious

def check_geo_anomaly(domain, expected_country="US"):
    ip = get_dns_response(domain)
    country = geoip_lookup(ip)
    
    if country != expected_country:
        if country in suspicious_countries:
            alert(f"{domain} now in {country}, expected {expected_country}")
```

---

## **Defense Strategies**

### **For Individuals:**

#### **1. Use Encrypted DNS (DoH/DoT)**
```json
// Firefox DoH settings (about:config)
"network.trr.mode": 3,
"network.trr.uri": "https://cloudflare-dns.com/dns-query"

// Android Private DNS (DoT)
Settings → Network & Internet → Private DNS
Hostname: dns.google
```

#### **2. Router Security**
```bash
# Router hardening checklist:
1. Change default admin password
2. Disable remote administration
3. Update firmware regularly
4. Disable UPnP unless needed
5. Use WPA3 encryption
6. Log DNS changes
```

#### **3. Hosts File Protection**
```powershell
# Windows: Make hosts file read-only
attrib +R C:\Windows\System32\drivers\etc\hosts

# Linux: Use immutable flag
sudo chattr +i /etc/hosts
```

#### **4. VPN Usage**
- Encrypts ALL traffic including DNS
- Prevents local network hijacking
- Choose reputable VPN provider

### **For Organizations:**

#### **1. DNS Firewall/Filtering**
```yaml
# Example: Cisco Umbrella/OpenDNS
# Benefits:
# - Blocks malicious domains
# - Logs all DNS queries
# - Prevents hijacking via policy enforcement
# - Integrates with Active Directory
```

#### **2. DNSSEC Enforcement**
```bash
# Force DNSSEC validation on all resolvers
# BIND example:
options {
    dnssec-validation auto;
    dnssec-enable yes;
};
```

#### **3. Network Segmentation**
```python
# Separate critical infrastructure DNS
# - Internal DNS for servers
# - External DNS for users
# - Guest network with different DNS
# - IoT network with restricted DNS
```

#### **4. Monitoring & Alerting**
```bash
# Monitor DNS server logs
tail -f /var/log/named/queries.log

# Alert on:
# - DNS config changes
# - Unusual query patterns
# - Failed DNSSEC validation
# - Geographic anomalies
```

### **For Domain Owners:**

#### **1. Registrar Security**
```
1. Enable 2FA on registrar account
2. Use registrar lock (prevents transfers)
3. Monitor WHOIS changes
4. Use privacy protection
5. Choose reputable registrar
```

#### **2. DNS Monitoring Services**
```bash
# Services that monitor your DNS records:
# - DNSimple
# - DNS Spy
# - Pingdom
# - UptimeRobot

# Get alerts if:
# - DNS records change
# - Nameservers change
# - Domain expires soon
```

#### **3. Split DNS Architecture**
```yaml
# Use different providers for redundancy:
Authoritative DNS: Cloudflare + Route53
Registrar: Namecheap (with 2FA)
DNS Monitoring: DNSimple alerts
```

---

## **Case Studies**

### **Case 1: Sea Turtle Campaign (2019)**
**Target:** Government and critical infrastructure in Middle East  
**Technique:** Registrar-level DNS hijacking  
**Attribution:** Likely nation-state (Turkey suspected)

**Attack Flow:**
1. Compromise email accounts of domain administrators
2. Use "forgot password" at registrar
3. Change NS records to attacker-controlled servers
4. Create SSL certificates for domains (Let's Encrypt)
5. Man-in-the-middle email, VPN, government portals

**Impact:**
- 40+ organizations across 13 countries
- Complete compromise of secure communications
- Lasted months before detection

### **Case 2: Brazilian Banking Attacks (2016-2019)**
**Target:** Major Brazilian banks  
**Technique:** ISP-level DNS hijacking  

**Modus Operandi:**
1. Bribe or compromise ISP technicians
2. Change DNS settings at ISP level
3. Redirect specific bank domains to phishing sites
4. Steal credentials during business hours
5. Transfer funds immediately

**Losses:** Estimated $3.5+ billion

### **Case 3: MyEtherWallet DNS Hijack (2018)**
**Combined attack:** BGP hijack + DNS poisoning

```
1. Hijack BGP routes for AWS IP space
2. AWS Route53 DNS queries now go to attacker
3. Poison DNS for myetherwallet.com
4. Users see legitimate-looking site
5. Ethereum private keys stolen
```

**Loss:** $150,000+ in cryptocurrency

---

## **Incident Response Plan**

### **Step 1: Detection & Verification**
```bash
# Quick verification commands
# Check current DNS servers:
nslookup
> server

# Check specific domain from multiple sources:
dig @8.8.8.8 bank.com
dig @1.1.1.1 bank.com

# Check router DNS:
# Login to 192.168.1.1 → check DNS settings
```

### **Step 2: Containment**
```
1. Disconnect affected systems from network
2. Reset router to factory defaults
3. Change ALL passwords (starting with email)
4. Scan for malware on all devices
5. Notify ISP if large-scale attack
```

### **Step 3: Eradication**
```powershell
# Windows: Reset DNS settings
netsh interface ip set dns "Ethernet" dhcp
netsh winsock reset

# Remove malware
Run: Malwarebytes, HitmanPro, Windows Defender Offline

# Check for persistence mechanisms
schtasks /query | findstr "dns"
Get-WmiObject -Namespace root\Subscription -Class __EventFilter
```

### **Step 4: Recovery**
```
1. Update all devices
2. Reconfigure security settings
3. Monitor for recurrence
4. Educate users
```

### **Step 5: Prevention**
```yaml
Implement:
- Encrypted DNS (DoH/DoT)
- DNSSEC validation
- Regular security audits
- Employee training
- Network monitoring
```

---

## **Advanced Threats & Future Trends**

### **1. AI-Powered Hijacking**
```python
# Machine learning to:
# - Identify high-value targets
# - Evade detection systems
# - Mimic legitimate traffic patterns
# - Optimize attack timing
```

### **2. 5G/Edge Computing Threats**
- More distributed DNS infrastructure
- New attack surfaces at edge
- Faster propagation possible

### **3. Quantum Threats to DNS**
- Current encryption vulnerable to quantum computers
- Need quantum-resistant algorithms
- DNSSEC may need complete overhaul

### **4. IoT Botnets for DNS Attacks**
- Mirai variant targeting routers
- Can create massive DNS hijacking botnets
- Difficult to remediate (users don't update IoT)

---

## **Legal & Regulatory Aspects**

### **Government-Mandated Hijacking:**
- **China:** Great Firewall (legal under local law)
- **Russia:** Sovereign Runet (legal requirement)
- **Iran:** National Information Network (legal)
- **EU:** DNS filtering for copyright enforcement (discussed)

### **Corporate Responsibility:**
- **GDPR:** DNS logs as personal data
- **HIPAA:** Medical records protection
- **PCI DSS:** Financial data security
- **SOX:** Financial reporting integrity

### **Law Enforcement:**
- **Court orders** for DNS redirection (wiretapping)
- **Sinkholing** botnets (FBI operation)
- **Asset seizure** via domain takedowns

---

## **Best Practices Summary**

### **For Everyone:**
✅ Use encrypted DNS (DoH/DoT)  
✅ Keep devices updated  
✅ Use strong, unique passwords  
✅ Enable 2FA everywhere  
✅ Be skeptical of unexpected redirects  

### **For Network Admins:**
✅ Implement DNSSEC  
✅ Monitor DNS traffic  
✅ Segment networks  
✅ Use DNS filtering  
✅ Regular security audits  

### **For Domain Owners:**
✅ Registrar security (2FA, lock)  
✅ DNS monitoring service  
✅ Regular backups of DNS zones  
✅ Incident response plan  

### **For Developers:**
✅ Implement certificate pinning  
✅ Use DNS-over-HTTPS in apps  
✅ Validate SSL certificates rigorously  
✅ Monitor for domain takovers  

---

## **The Bottom Line**

DNS hijacking is **insidious** because:
1. **Stealthy:** Users see correct URLs (just wrong IPs)
2. **High-impact:** Can compromise all communications
3. **Persistent:** Until manually fixed
4. **Profitable:** Drives phishing, fraud, espionage

**The single most effective defense:** **Encrypted DNS (DoH/DoT)**. It protects against local, router, and MITM hijacking.

**Remember:** If your DNS is compromised, **ALL your internet activity is compromised**. No amount of HTTPS, VPNs, or firewalls can save you if the phonebook itself is malicious.

**Final thought:** In 2024, **not using encrypted DNS is like sending postcards instead of sealed letters**. The technology exists—use it!
