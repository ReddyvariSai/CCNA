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

| Port | Protocol | Service Name | Full Form | How It Works in Networking |
|-----:|:--------:|-------------|----------|-----------------------------|
| 20 | TCP | FTP (Data) | File Transfer Protocol |  Transfers actual file data from server to client |
| 21 | TCP | FTP (Control) | File Transfer Protocol | Establishes FTP connection and sends commands |
| 22 | TCP | SSH | Secure Shell | Secure remote login and encrypted command execution |
| 23 | TCP | Telnet | Teletype Network | Remote login without encryption |
| 25 | TCP | SMTP | Simple Mail Transfer Protocol | Sends emails from client to mail server |
| 53 | TCP/UDP | DNS | Domain Name System | Converts domain names into IP addresses |
| 67 | UDP | DHCP (Server) | Dynamic Host Configuration Protocol | Assigns IP address to client |
| 68 | UDP | DHCP (Client) | Dynamic Host Configuration Protocol | Requests IP configuration from DHCP server |
| 69 | UDP | TFTP | Trivial File Transfer Protocol | Transfers small files without authentication |
| 80 | TCP | HTTP | HyperText Transfer Protocol |  Transfers web pages in plain text |
| 110 | TCP | POP3 | Post Office Protocol Version 3 | Downloads emails from mail server |
| 119 | TCP | NNTP | Network News Transfer Protocol | Transfers Usenet news articles |
| 123 | UDP | NTP | Network Time Protocol | Synchronizes system time over network |
| 137 | UDP | NetBIOS | Network Basic Input Output System | Resolves NetBIOS names to IP addresses |
| 138 | UDP | NetBIOS Datagram | Network Basic Input Output System | Sends NetBIOS datagram messages |
| 139 | TCP | NetBIOS Session | Network Basic Input Output System | Provides file and printer sharing sessions |
| 143 | TCP | IMAP | Internet Message Access Protocol |  Accesses emails directly on mail server |
| 161 | UDP | SNMP | Simple Network Management Protocol | Monitors and manages network devices |
| 162 | UDP | SNMP Trap | Simple Network Management Protocol | Sends alerts from devices to SNMP manager |
| 179 | TCP | BGP | Border Gateway Protocol | Exchanges routing information between ISPs |
| 389 | TCP/UDP | LDAP | Lightweight Directory Access Protocol | Accesses and manages directory services |
| 443 | TCP | HTTPS | HyperText Transfer Protocol Secure | Secure encrypted web communication |
| 445 | TCP | SMB | Server Message Block | File and printer sharing in Windows networks |
| 514 | UDP | Syslog | System Logging Protocol | Sends system log messages to log server |
| 520 | UDP | RIP | Routing Information Protocol | Exchanges routing information between routers |

---
## 🧠 Simple Understanding

- **Client** uses a random port  
- **Server** listens on a **well-known port**
- Data is sent using **IP + Port Number**

Example:


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

