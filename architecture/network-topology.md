# 🌐 Network Topology

## Lab Network Diagram

```
┌─────────────────────────────────────────────────────────┐
│                   Windows Host Machine                   │
│                    (VirtualBox Host)                     │
└──────────────────────┬──────────────────────────────────┘
                       │
              VirtualBox NAT Network
              (NatNetwork: 10.0.2.0/24)
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ Ubuntu VM    │ │ Ubuntu VM    │ │ Windows VM   │
│              │ │              │ │              │
│ Wazuh Manager│ │ Wazuh Agent  │ │ Wazuh Agent  │
│ + Dashboard  │ │              │ │              │
│ + Indexer    │ │              │ │              │
│              │ │              │ │              │
│ 10.0.2.5     │ │ 10.0.2.4     │ │ 10.0.2.15    │
└──────┬───────┘ └──────┬───────┘ └──────┬───────┘
       │                │                │
       │◄───────────────┘                │
       │         Agent (port 1514)       │
       │◄────────────────────────────────┘
       │         Agent (port 1514)
       │
       ▼
┌──────────────┐
│  VirusTotal  │
│  API (cloud) │
└──────────────┘
```

## Port Reference

| Port | Protocol | Purpose |
|------|----------|---------|
| 1514 | TCP/UDP | Agent communication |
| 1515 | TCP | Agent registration |
| 443  | HTTPS | Wazuh Dashboard |
| 9200 | TCP | Wazuh Indexer API |

## VM Specifications

| VM | OS | RAM | Role |
|----|----|-----|------|
| Ubuntu Manager | Ubuntu 22.04 | 4GB+ | Wazuh Manager, Indexer, Dashboard |
| Ubuntu Agent | Ubuntu 22.04 | 2GB | Wazuh Agent (biometrics) |
| Windows Agent | Windows 10/11 | 2GB | Wazuh Agent (DESKTOP-BDG3OLC) |
