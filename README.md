# 🔐 WireGuard VPN Home Lab (Proxmox)

## 📌 Overview
Designed and deployed a secure WireGuard VPN on a Proxmox server to enable encrypted remote access to a home lab environment. The solution supports multiple clients and allows secure connectivity to internal virtual machines, including a Kali Linux system.

This project simulates real-world enterprise remote access scenarios and demonstrates hands-on experience in networking, security, and troubleshooting.
This setup enables secure remote access to internal services without exposing them directly to the internet, reducing attack surface and improving network security.
---

## 🧠 Objectives
- Build a secure remote access solution
- Implement key-based VPN authentication
- Enable access to internal network resources
- Monitor VPN traffic and connections
- Troubleshoot real-world networking issues

---

## 🏗️ Architecture
```
Client Devices (Mac, iPhone)
        │
        │  Encrypted Tunnel (WireGuard)
        ▼
Public IP (Router Port Forwarding)
        │
        ▼
Proxmox Server (WireGuard VPN - wg0)
        │
        ▼
Internal Network (192.168.1.0/24)
        │
        └── Kali Linux VM (192.168.1.161)
```

This architecture ensures that external clients securely access internal resources without exposing services directly to the public internet.

---

## ⚙️ Technologies Used
- WireGuard VPN
- Proxmox VE
- Kali Linux
- Linux Networking (iptables, routing)
- tcpdump (packet analysis)
- vnStat (traffic monitoring)

---

## 🔑 Key Features
- 🔐 Secure VPN using public/private key cryptography
- 👥 Multi-client support (Mac + iPhone)
- 🌐 Access to internal network (192.168.1.0/24)
- 🖥️ Remote SSH access to Kali VM
- 📊 Bandwidth monitoring with vnStat
- 🧪 Packet analysis with tcpdump

---

## 🛠️ Setup Summary

### 1. WireGuard Server Configuration (Proxmox)
- Installed WireGuard
- Configured `wg0.conf`
- Enabled IP forwarding
- Configured NAT:
```ini
[Interface]
Address = 10.0.0.1/24
ListenPort = 51820
PrivateKey = <REDACTED>
PostUp = iptables -t nat -A POSTROUTING -o vmbr0 -j MASQUERADE
PostDown = iptables -t nat -D POSTROUTING -o vmbr0 -j MASQUERADE

[Peer]
PublicKey = <REDACTED>
AllowedIPs = 10.0.0.2/32
```

### 2. Port Forwarding
Forwarded UDP port 51820 to Proxmox server (192.168.1.88)

### 3. Client Configuration
Each client configured with:
- Unique key pair
- Unique VPN IP (e.g., 10.0.0.2, 10.0.0.3)
- Persistent keepalive

### 4. Multi-Client Setup
Added multiple peers:
```ini
[Peer]
PublicKey = CLIENT_PUBLIC_KEY
AllowedIPs = 10.0.0.X/32
```

> 🔒 **Security Note:** Each peer is assigned a `/32` AllowedIPs address,
> scoping them strictly to their own VPN IP. This prevents clients from
> seeing or spoofing each other's tunnel traffic — an important isolation
> practice in multi-client VPN deployments.

### 5. Internal Network Access
Enabled routing to 192.168.1.0/24 and verified connectivity to Kali VM via:
```bash
ping 192.168.1.161
ssh francisca@192.168.1.161
```

---

## 🐛 Troubleshooting (Real-World Debugging)
This project involved extensive troubleshooting to resolve real-world networking issues.

| Issue | Cause | Fix |
|-------|-------|-----|
| ❌ No handshake | Key mismatch | Regenerated and properly mapped keys |
| ❌ VPN connects but no access | Missing NAT or routing | Added MASQUERADE rule |
| ❌ Connection failure | Port forwarding misconfiguration | Verified using `tcpdump -i vmbr0 udp port 51820` |
| ❌ SSH Permission Denied | Incorrect credentials | Verified user and reset password |

---

## 📊 Monitoring & Logging

**🔹 Live Tunnel Status**
```bash
wg
```

**🔹 Bandwidth Monitoring**
```bash
vnstat -i wg0
vnstat -l -i wg0
```

**🔹 Packet Capture**
```bash
tcpdump -i vmbr0 udp port 51820
```

---

## 📸 Screenshots
These screenshots validate successful VPN deployment, internal network access, and traffic monitoring.

### 🔐 VPN Handshake
<img width="364" height="88" alt="handshake" src="https://github.com/user-attachments/assets/5300f48d-5066-4df8-abb8-230d203ca53a" />

### 🐉 SSH into Kali VM
<img width="601" height="386" alt="kali-ssh" src="https://github.com/user-attachments/assets/ada6aa17-bbd7-41ec-a7e1-ebda9dec2c7a" />

### 📡 Internal Network Connectivity
<img width="601" height="386" alt="ping Kali internal network connectivity" src="https://github.com/user-attachments/assets/1af0c809-c27b-483d-92f5-d74e5e0b4d91" />

### 📊 Traffic Monitoring (vnStat)
<img width="1263" height="851" alt="vnstat" src="https://github.com/user-attachments/assets/26323566-7aeb-4411-81e3-3723e01decd8" />

---

## 🎯 Impact
This project demonstrates the ability to design, deploy, and troubleshoot secure network infrastructure in a real-world lab environment.

---

## 🚀 Roadmap

| Priority | Improvement | Purpose |
|----------|-------------|---------|
| High | Firewall rules (iptables/nftables) | Segment VPN clients from internal network |
| High | Intrusion detection (Suricata/Snort) | Alert on suspicious tunnel traffic |
| Medium | SIEM integration (Elastic Stack) | Centralized log analysis and dashboards |
| Medium | Automated key rotation | Enforce key expiry policy |
