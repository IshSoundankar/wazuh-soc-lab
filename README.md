# Wazuh SOC Lab — Multi-Agent SIEM Setup with VirusTotal Integration

A hands-on Security Operations Center (SOC) home lab built using **Wazuh 4.7.5** with a multi-VM setup, File Integrity Monitoring (FIM), and VirusTotal malware detection integration.

---

## Lab Architecture

| VM | Role | OS | IP |
|---|---|---|---|
| Ubuntu Server | Wazuh Manager + Dashboard + Indexer | Ubuntu 22.04 | `10.0.2.5` |
| Ubuntu Agent | Wazuh Agent | Ubuntu 22.04 | `10.0.2.4` |
| Windows Agent | Wazuh Agent | Windows 10/11 | `10.0.2.15` |

All VMs are connected via **VirtualBox NAT Network** (`10.0.2.0/24`).

---

## Tech Stack

- **Wazuh 4.7.5** — SIEM & XDR platform
- **VirtualBox** — Virtualization
- **Ubuntu 22.04** — Manager & Agent OS
- **Windows 10/11** — Windows Agent
- **VirusTotal API** — Malware detection
- **OpenSearch** — Wazuh Indexer backend

---

## Features Demonstrated

- Multi-agent Wazuh deployment (Linux + Windows)
- File Integrity Monitoring (FIM) with real-time alerts
- VirusTotal API integration for malicious file detection
- EICAR test file detection
- Security Events monitoring via Wazuh Dashboard
- NAT Network VM communication setup

---

## Quick Start

See the full setup guide: [docs/setup-guide.md](docs/setup-guide.md)

### Prerequisites
- VirtualBox installed on host
- 3 VMs (2x Ubuntu 22.04, 1x Windows 10/11)
- Minimum 8GB RAM on host machine
- VirusTotal free account + API key

---

## Repository Structure

```
wazuh-soc-lab/
├── README.md
├── architecture/
│   └── network-topology.md
├── configs/
│   ├── manager-ossec.conf
│   ├── agent-ubuntu-ossec.conf
│   └── agent-windows-ossec.conf
├── docs/
│   ├── setup-guide.md
│   └── virustotal-integration.md
└── screenshots/
```

---

## Key Findings

- Wazuh agent version must be **equal to or lower** than the manager version
- VirtualBox **NAT Network** (not NAT) is required for inter-VM communication
- Wazuh Indexer requires JVM heap size tuning (`-Xms1g -Xmx1g`) on low-RAM VMs
- Swap space is required for stable Wazuh Indexer operation

---

## Author

**Ish** — MEng Cybersecurity, University of Limerick  
[LinkedIn](https://www.linkedin.com/in/ish-soundankar-7070592b4/) | [GitHub](https://github.com/IshSoundankar)

---

## License

MIT License — feel free to use this lab setup for educational purposes.
