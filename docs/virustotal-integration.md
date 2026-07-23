# 🦠 VirusTotal Integration Guide

## Overview
This guide covers integrating VirusTotal with Wazuh for automatic malicious file detection using File Integrity Monitoring (FIM).

---

## Prerequisites
- Wazuh Manager running
- VirusTotal free account + API key (https://www.virustotal.com)
- Free tier: ~500 file scans/day

---

## Step 1 — Add VirusTotal Integration to Manager

Edit Wazuh Manager config:
```bash
sudo nano /var/ossec/etc/ossec.conf
```

Add before `</ossec_config>`:
```xml
<integration>
  <name>virustotal</name>
  <api_key>YOUR_VIRUSTOTAL_API_KEY</api_key>
  <rule_id>554, 553, 550</rule_id>
  <alert_format>json</alert_format>
</integration>
```

---

## Step 2 — Configure FIM on Ubuntu Agent

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Add inside `<syscheck>`:
```xml
<directories realtime="yes" check_all="yes">/home/user/test</directories>
```

Restart agent:
```bash
sudo systemctl restart wazuh-agent
```

---

## Step 3 — Configure FIM on Windows Agent

Edit `C:\Program Files (x86)\ossec-agent\ossec.conf`:
```xml
<directories realtime="yes" check_all="yes">C:\Users\imish\Desktop\test</directories>
```

Restart agent:
```powershell
NET STOP WazuhSvc
NET START WazuhSvc
```

---

## Step 4 — Restart Wazuh Manager
```bash
sudo systemctl restart wazuh-manager
```

---

## Step 5 — Create Test Directories

**Ubuntu Agent:**
```bash
mkdir /home/user/test
```

**Windows Agent:**
```powershell
mkdir C:\Users\imish\Desktop\test
```

---

## Step 6 — Download EICAR Test File (Safe Malware Sample)

**Ubuntu Agent:**
```bash
cd /home/user/test
wget https://secure.eicar.org/eicar.com.txt
echo "This is a normal file" > normal.txt
```

**Windows Agent:**
```powershell
cd C:\Users\imish\Desktop\test
Invoke-WebRequest -Uri "https://secure.eicar.org/eicar.com.txt" -OutFile "eicar.com.txt"
echo "This is a normal file" > normal.txt
```

---

## Step 7 — Check Alerts in Wazuh Dashboard

1. Open browser → `https://10.0.2.5`
2. Login with admin credentials
3. Go to **Modules → Integrity Monitoring** → Check FIM events
4. Go to **Security Events** → Filter by `virustotal`

---

## Expected Alerts

| File | Expected Result |
|---|---|
| `eicar.com.txt` | 🔴 Malicious — VirusTotal alert triggered |
| `normal.txt` | 🟢 Clean — No alert |

---

## Auto-Delete Malicious Files (Optional)

For automatic deletion, see:
https://documentation.wazuh.com/current/proof-of-concept-guide/detect-remove-malware-virustotal.html

Add an active response rule to `ossec.conf` on the manager to automatically remove files flagged by VirusTotal.
