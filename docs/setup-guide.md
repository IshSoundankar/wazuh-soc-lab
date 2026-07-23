# 📖 Full Setup Guide — Wazuh SOC Lab

## Table of Contents
1. [VirtualBox Network Setup](#1-virtualbox-network-setup)
2. [Wazuh Manager Installation](#2-wazuh-manager-installation)
3. [Ubuntu Agent Setup](#3-ubuntu-agent-setup)
4. [Windows Agent Setup](#4-windows-agent-setup)
5. [Verify All Agents](#5-verify-all-agents)
6. [Troubleshooting](#6-troubleshooting)

---

## 1. VirtualBox Network Setup

### Create NAT Network
1. Open VirtualBox → `File → Tools → Network Manager → NAT Networks → Create`
2. Set:
   - **Name:** `NatNetwork`
   - **IPv4 Prefix:** `10.0.2.0/24`
   - **Enable DHCP:** ✅

### Assign All 3 VMs to NAT Network
For each VM (while shut down):
```
Settings → Network → Adapter 1
├── Enable Network Adapter ✅
├── Attached to: NAT Network
└── Name: NatNetwork
```

### Verify Connectivity
After starting all VMs, confirm IPs:

**Ubuntu Manager:**
```bash
ip a | grep inet
```

**Ubuntu Agent:**
```bash
ip a | grep inet
```

**Windows Agent:**
```cmd
ipconfig
```

Test ping between all VMs before proceeding.

---

## 2. Wazuh Manager Installation

> Follow the official Wazuh documentation for manager installation:
> https://documentation.wazuh.com/current/installation-guide/wazuh-server/index.html

### Fix Wazuh Indexer JVM Memory Issue
If the indexer fails to start due to OOM (Out of Memory):

**Add Swap Space:**
```bash
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

**Reduce JVM Heap Size:**
```bash
sudo nano /etc/wazuh-indexer/jvm.options
```
Change:
```
-Xms6250m
-Xmx6250m
```
To:
```
-Xms1g
-Xmx1g
```

**Start Services in Order:**
```bash
sudo systemctl start wazuh-indexer
sudo systemctl start wazuh-manager
sudo systemctl start wazuh-dashboard
```

---

## 3. Ubuntu Agent Setup

### Install Wazuh Agent
```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh.gpg

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list

sudo apt update
sudo WAZUH_MANAGER="10.0.2.5" WAZUH_AGENT_NAME="ubuntu-agent" apt install wazuh-agent=4.7.5-1 -y
```

> ⚠️ **Important:** Agent version must be equal to or lower than the manager version.

### Configure Manager IP
```bash
sudo nano /var/ossec/etc/ossec.conf
```
Ensure the `<client>` section has:
```xml
<client>
  <server>
    <address>10.0.2.5</address>
    <port>1514</port>
    <protocol>tcp</protocol>
  </server>
</client>
```

### Start Agent
```bash
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

---

## 4. Windows Agent Setup

### Download Wazuh Agent 4.7.5
Download from browser:
```
https://packages.wazuh.com/4.x/windows/wazuh-agent-4.7.5-1.msi
```

### Install via GUI
Double-click the MSI and follow the installer:
- **Manager IP:** `10.0.2.5`
- **Agent Name:** `windows-agent`

### Register Agent with Manager
On **Ubuntu Manager**, add the Windows agent:
```bash
sudo /var/ossec/bin/manage_agents
```
- Press `A` → Add agent
- Name: `windows-agent`
- IP: `10.0.2.15`
- Press `Q` to quit

On **Windows Agent**, register via CLI:
```powershell
cd "C:\Program Files (x86)\ossec-agent"
.\agent-auth.exe -m 10.0.2.5
```

### Start Windows Agent Service
```powershell
NET START WazuhSvc
```

---

## 5. Verify All Agents

On **Ubuntu Manager**:
```bash
sudo /var/ossec/bin/agent_control -l
```

Expected output:
```
ID: 000, Name: ubuntu (server), IP: 127.0.0.1, Active/Local
ID: 004, Name: biometrics,      IP: any,        Active
ID: 005, Name: DESKTOP-BDG3OLC, IP: any,        Active
```

---

## 6. Troubleshooting

| Issue | Cause | Fix |
|---|---|---|
| Agent version mismatch | Agent newer than manager | Install matching agent version |
| Never connected | Network/firewall issue | Check NAT Network settings |
| Indexer OOM killed | Not enough RAM/swap | Add swap + reduce JVM heap |
| ping timeout between VMs | VMs on NAT (not NAT Network) | Switch to NAT Network mode |
| Service not found | Install failed | Reinstall with GUI installer |
