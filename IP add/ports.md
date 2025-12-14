# Ports in Networking

## 📌 What is a Port?

A **Port** is a **logical communication endpoint** used by a computer to identify **which application or service** should receive network data.

- Ports work at **Transport Layer (Layer 4)** of the OSI Model.
- Port numbers are used with **IP addresses**.
- A port helps one system run **multiple network services at the same time**.

### 🧠 Simple Example

- IP Address = House Address  
- Port Number = Room Number  

Example:
192.168.1.10:80
- `192.168.1.10` → IP Address  
- `80` → HTTP (Web service)

---

## 🔢 How Many Ports Are There?

Ports are **16-bit numbers**, so the total number of ports is:

2^16 = 65,536 ports

### ✅ Total Ports:
**0 – 65535**

---

## 📦 Port Number Ranges

| Port Range | Name | Description |
|-----------:|------|------------|
| 0 – 1023 | Well-Known Ports | Standard services (HTTP, FTP, SSH) |
| 1024 – 49151 | Registered Ports | User & application services |
| 49152 – 65535 | Dynamic / Ephemeral Ports | Temporary client ports |

---


# Well-Known Ports in Networking (0–1023)

## 📌 Total Number of Ports

- **Port Range:** 0 – 1023  
- **Total Ports:** **1024**
- **Name:** Well-Known Ports  
- **Assigned By:** IANA (Internet Assigned Numbers Authority)

These ports are reserved for **standard networking services and protocols**.

---


So, there are **1024 well-known ports**.

---

## 🔐 Important Well-Known Ports with Names & Full Forms

| Port | Protocol | Service Name | Full Form |
|-----:|:--------:|-------------|----------|
| 20 | TCP | FTP (Data) | File Transfer Protocol |
| 21 | TCP | FTP (Control) | File Transfer Protocol |
| 22 | TCP | SSH | Secure Shell |
| 23 | TCP | Telnet | Teletype Network |
| 25 | TCP | SMTP | Simple Mail Transfer Protocol |
| 53 | TCP/UDP | DNS | Domain Name System |
| 67 | UDP | DHCP (Server) | Dynamic Host Configuration Protocol |
| 68 | UDP | DHCP (Client) | Dynamic Host Configuration Protocol |
| 69 | UDP | TFTP | Trivial File Transfer Protocol |
| 80 | TCP | HTTP | HyperText Transfer Protocol |
| 110 | TCP | POP3 | Post Office Protocol Version 3 |
| 119 | TCP | NNTP | Network News Transfer Protocol |
| 123 | UDP | NTP | Network Time Protocol |
| 137 | UDP | NetBIOS | Network Basic Input Output System |
| 138 | UDP | NetBIOS Datagram | Network Basic Input Output System |
| 139 | TCP | NetBIOS Session | Network Basic Input Output System |
| 143 | TCP | IMAP | Internet Message Access Protocol |
| 161 | UDP | SNMP | Simple Network Management Protocol |
| 162 | UDP | SNMP Trap | Simple Network Management Protocol |
| 179 | TCP | BGP | Border Gateway Protocol |
| 389 | TCP/UDP | LDAP | Lightweight Directory Access Protocol |
| 443 | TCP | HTTPS | HyperText Transfer Protocol Secure |
| 445 | TCP | SMB | Server Message Block |
| 514 | UDP | Syslog | System Logging Protocol |
| 520 | UDP | RIP | Routing Information Protocol |

---

## 📘 Notes

- Not all **1024 ports** have individual names.
- Only **commonly used ports** are standardized and named.
- These ports usually require **administrator/root privileges**.
- Most network attacks begin by scanning **well-known ports**.

---

## 📝 Exam & Interview Ready Summary

- **0–1023 = 1024 ports**
- Called **Well-Known Ports**
- Used by **standard networking services**
- Essential for **CCNA, Networking, and Cybersecurity**

---

## 📚 Author

Prepared for learning **Networking, CCNA, and Cybersecurity basics**.

