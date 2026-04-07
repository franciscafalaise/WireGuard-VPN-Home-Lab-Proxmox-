# 🔐 WireGuard VPN Home Lab (Proxmox)

## 📌 Overview
Deployed a secure WireGuard VPN on a Proxmox server to enable remote access to a home lab environment. The setup supports multiple clients and allows secure connectivity to internal virtual machines, including a Kali Linux system.

---

## 🧠 Objectives
- Build a secure remote access solution
- Implement key-based VPN authentication
- Enable access to internal network resources
- Monitor VPN traffic and connections
- Troubleshoot real-world networking issues

---

## 🏗️ Architecture

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
```bash
iptables -t nat -A POSTROUTING -o vmbr0 -j MASQUERADE
2. Port Forwarding
Forwarded UDP port 51820 to Proxmox server (192.168.1.88)
3. Client Configuration
Each client configured with:
Unique key pair
Unique VPN IP (e.g., 10.0.0.2, 10.0.0.3)
Persistent keepalive
4. Multi-Client Setup
Added multiple peers:
[Peer]
PublicKey = CLIENT_PUBLIC_KEY
AllowedIPs = 10.0.0.X/32
5. Internal Network Access
Enabled routing to 192.168.1.0/24
Verified connectivity to Kali VM via:
ping 192.168.1.161
ssh francisca@192.168.1.161
Troubleshooting (Real-World Debugging)
❌ Issue: No handshake
Cause: Key mismatch
Fix: Regenerated and properly mapped keys
❌ Issue: VPN connects but no access
Cause: Missing NAT or routing
Fix: Added MASQUERADE rule
❌ Issue: Connection failure
Cause: Port forwarding misconfiguration
Fix: Verified using tcpdump:
tcpdump -i vmbr0 udp port 51820
❌ Issue: SSH Permission Denied
Cause: Incorrect credentials
Fix: Verified user and reset password
📊 Monitoring & Logging
🔹 Live Tunnel Status
wg
🔹 Bandwidth Monitoring
vnstat -i wg0
vnstat -l -i wg0
🔹 Packet Capture
tcpdump -i vmbr0 udp port 51820

## 📸 Screenshots

### 🔐 VPN Handshake
<img width="364" height="88" alt="handshake" src="https://github.com/user-attachments/assets/8df6f0b2-eee2-4c21-b9cf-a9e71d45af40" />

### 🐉 SSH into Kali VM
<img width="601" height="386" alt="kali-ssh" src="https://github.com/user-attachments/assets/a919cc3d-94f4-4af8-b01f-2475222549df" />

### 📡 Internal Network Connectivity
<img width="601" height="386" alt="ping Kali" src="https://github.com/user-attachments/assets/e041bd4e-694e-4eff-8e5c-5163383e125b" />

### 📊 Traffic Monitoring (vnStat)
<img width="1263" height="851" alt="vnstat" src="https://github.com/user-attachments/assets/ea060c96-2d70-446e-9236-0ec20d43c7b0" />


🚀 Future Improvements
Add firewall rules for segmentation
Integrate with SIEM (Elastic Stack)
Implement key rotation policy
Add intrusion detection monitoring
