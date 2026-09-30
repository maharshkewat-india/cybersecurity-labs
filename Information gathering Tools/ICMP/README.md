# ICMP Information Gathering — Cybersecurity Labs

This folder contains practical lab resources for practicing **ICMP-based information gathering** and network reconnaissance using tools like Nmap against real lab targets (Metasploitable, Windows 7).

## Lab Files

| # | File | Description |
|---|------|-------------|
| 1 | `Using METASPOITABLE ICMP.txt.txt` | ICMP scanning examples against a Metasploitable target — demonstrates host discovery via ICMP and open port enumeration |
| 2 | `non echo sweep in meta2.txt.txt` | Detailed ICMP Echo Request/Reply analysis with `--packet-trace` — shows why a host is marked up and how to read TTL for OS fingerprinting |
| 3 | `using window 7.txt.txt` | ICMP scanning examples against a Windows 7 host — comparing Windows TTL (128) and service fingerprints |
| 4 | `using TCP & UDP sweep.txt` | Combined TCP/UDP sweep examples — port discovery using both protocol types |
| 5 | `README.md` | This file — overview and guide for all ICMP lab resources |

## Key Concepts Covered

- **ICMP Echo Request (type=8) / Echo Reply (type=0)** — basic host reachability check
- **Nmap `-PE` flag** — sends ICMP Echo instead of default ARP ping
- **TTL-based OS fingerprinting** — TTL=128 → Windows, TTL=64 → Linux
- **`--packet-trace`** — inspect raw sent/received packets for learning
- **`--reason`** — see why Nmap marks a host up or down
- **`--disable-arp-ping`** — force ICMP-only discovery

## Quick Reference

```bash
# Basic ICMP ping scan
nmap -PE <target>

# ICMP ping with packet trace and reason
nmap -PE -sn <target> --reason --packet-trace --disable-arp-ping

# ICMP scan without DNS resolution
nmap -PE -sn <target> -n
```

## Important Notes

- These labs are for **educational use only** — only scan networks you own or have explicit permission to test.
- All examples use lab targets (Metasploitable, Windows 7 in VirtualBox) on private IP ranges (10.0.2.x).
- CRLF→LF conversion warnings are normal on Windows → Linux cross-platform repos — they don't affect functionality.

---
*Created for cybersecurity labs educational use*
