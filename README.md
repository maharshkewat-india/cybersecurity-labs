# Nmap Network Vulnerability Assessment

An authorized-lab assessment documenting exposed services and Nmap-reported vulnerability indicators on `10.0.2.3`.

## Project Overview

This project records a service and vulnerability-indicator assessment performed with Nmap 7.99. Service/version detection identified listening TCP ports and reported service banners; selected NSE scripts reported vulnerability indicators. The results are from an educational lab report and are not a substitute for validating the installed software, configuration, and patch state on the host.

The intended scope is an authorized cybersecurity lab. Do not scan systems without permission.

## Objectives

- Inventory open TCP ports and identified services.
- Record service/version information reported by Nmap.
- Document direct NSE vulnerability findings separately from version-indexed references.
- Preserve the CVE identifiers and scores present in the report without treating every match as confirmed.
- Recommend defensive remediation and non-destructive verification.

## Features

- Source-grounded service inventory and findings.
- Clear separation of Nmap output, interpretation, and defensive advice.
- Dedicated TLS/SSL notes for the reported Diffie-Hellman, Logjam, POODLE, and CCS Injection indicators.
- Safe verification guidance for an authorized administrator.

## Lab Environment

| Item | Details |
| --- | --- |
| Target | `10.0.2.3` |
| Tool | Nmap 7.99 |
| Scan date | September 26, 2026, as recorded in the report |
| Scan type | TCP service/version detection and `vuln` NSE scripts |
| Privilege option | `--privileged` |
| Output format | Normal text (`-oN namp_scan.txt`) |
| Operating-system information | `Unix, Linux` (Nmap service information) |
| Lab environment | Not specified in scan report; this write-up is intended for an authorized lab |

## Scan Command

The command below is preserved exactly as recorded in the report:

```text
/usr/lib/nmap/nmap --privileged -sV --script vuln -oN namp_scan.txt 10.0.2.3
```

- `/usr/lib/nmap/nmap` invokes the Nmap executable at the reported path.
- `--privileged` tells Nmap to assume the process has privileges for its scan operations.
- `-sV` requests service and version detection.
- `--script vuln` selects NSE scripts in the `vuln` category. Script results can be indicative and require validation.
- `-oN namp_scan.txt` writes normal-format output to `namp_scan.txt`.
- `10.0.2.3` is the reported target.

## Results Summary

Nmap reported the host up with `0.00068s` latency, 977 closed TCP ports (reset), and 23 open TCP ports. The report's port inventory is summarized below; the version text follows the scan output.

| Port | Protocol | Service | Version / Nmap detail | Finding |
| --- | --- | --- | --- | --- |
| 21 | TCP | FTP | vsftpd 2.3.4 | NSE reported the vsftpd 2.3.4 backdoor as `VULNERABLE`; CVE-2011-2523 |
| 22 | TCP | SSH | OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0) | `vulners` returned version-indexed references; not independently confirmed |
| 23 | TCP | Telnet | Linux telnetd | Telnet exposed |
| 25 | TCP | SMTP | Postfix smtpd | NSE reported anonymous/weak DH, Logjam, and SSL POODLE indicators; see [findings](Nmap/documentation/findings.md#tls-ssl-findings) |
| 53 | TCP | domain | ISC BIND 9.4.2 | `vulners` returned version-indexed references; not independently confirmed |
| 80 | TCP | HTTP | Apache httpd 2.2.8 ((Ubuntu) DAV/2) | TRACE enabled; possible CSRF paths and enumerated paths; see findings |
| 111 | TCP | rpcbind | 2 (RPC #100000) | RPC services reported by `rpcinfo` |
| 139 | TCP | netbios-ssn | Samba smbd 3.X - 4.X (workgroup: WORKGROUP) | Service exposed |
| 445 | TCP | netbios-ssn | Samba smbd 3.X - 4.X (workgroup: WORKGROUP) | Service exposed |
| 512 | TCP | exec | netkit-rsh rexecd | Legacy remote execution service exposed |
| 513 | TCP | login | OpenBSD or Solaris rlogind | Legacy remote login service exposed |
| 514 | TCP | tcpwrapped | Not specified in scan report | Port open; service connection wrapped |
| 1099 | TCP | java-rmi | GNU Classpath grmiregistry | NSE reported a vulnerable default-configuration class-loading indicator |
| 1524 | TCP | bindshell | Metasploitable root shell | Nmap identified a root-shell service |
| 2049 | TCP | nfs | 2-4 (RPC #100003) | NFS exposed |
| 2121 | TCP | FTP | ProFTPD 1.3.1 | `vulners` returned version-indexed references; not independently confirmed |
| 3306 | TCP | mysql | MySQL 5.0.51a-3ubuntu5 | `vulners` returned version-indexed references; not independently confirmed |
| 5432 | TCP | postgresql | PostgreSQL DB 8.3.0 - 8.3.7 | NSE reported SSL POODLE, weak DH, and CCS Injection indicators |
| 5900 | TCP | vnc | VNC (protocol 3.3) | VNC exposed |
| 6000 | TCP | X11 | Access denied | X11 service detected; access denied by scan |
| 6667 | TCP | irc | UnrealIRCd | NSE reported it looked like a trojaned version |
| 8009 | TCP | ajp13 | Apache Jserv (Protocol v1.3) | AJP service exposed |
| 8180 | TCP | http | Apache Tomcat/Coyote JSP engine 1.1 | Slowloris check reported `LIKELY VULNERABLE`; CVE-2007-6750 |

## Vulnerability Findings

The direct NSE findings include the vsftpd backdoor, TLS weaknesses on ports 25 and 5432, an RMI class-loading indicator, a likely Slowloris denial-of-service condition on port 8180, and a suspected trojaned UnrealIRCd version. The scan also reports potentially interesting HTTP paths and possible CSRF forms; those observations are not proof of exploitable web vulnerabilities. See [detailed findings](Nmap/documentation/findings.md).

The `vulners` script associated many CVE references and scores with detected software versions. These are version-based database matches, not proof that each CVE applies to this host. Confirm exact package builds, vendor backports, and configuration before assigning applicability. See [detailed findings](Nmap/documentation/findings.md).

## Remediation

Prioritize isolating the lab host and disabling or replacing the explicitly vulnerable vsftpd and suspected UnrealIRCd services. Remove the root-shell listener and unnecessary legacy services; update supported software; and configure TLS to reject SSLv3, anonymous DH, export-grade ciphers, and weak DH groups. Restrict database, RPC, file-sharing, and administration ports to authorized networks. The complete prioritized plan and safe verification steps are in the [remediation guide](Nmap/remediation/remediation-guide.md).

## Repository Structure

```text
nmap-vulnerability-assessment/
├── README.md
├── reports/
│   └── nmap_scan.txt
├── evidence/
│   └── screenshots/
├── documentation/
│   └── findings.md
├── remediation/
│   └── remediation-guide.md
├── LICENSE
└── .gitignore
```

The report in this workspace is currently `Nmap/namp_scan.txt`, and the assessment documentation is in `Nmap/documentation/` and `Nmap/remediation/`. The tree above is a suggested layout for extracting this work into its own repository. If moving the report into `reports/`, review and sanitize the public copy first: the source includes a MAC address and hostnames in addition to the target IP. Keep an unmodified original only in an appropriately controlled location.

- `reports/` stores reviewed scan output.
- `evidence/screenshots/` stores sanitized screenshots.
- `documentation/` contains analysis and findings.
- `remediation/` contains prioritized defensive actions and verification notes.
- `LICENSE` states reuse terms; add one only after choosing a license.
- `.gitignore` excludes common local and sensitive artifacts.

## Evidence

For a portfolio, retain only necessary, sanitized evidence: reviewed Nmap output, screenshots with unrelated identifying details removed, assessment notes, and remediation verification results. Never publish passwords, private keys, tokens, or unnecessary personal or host-identifying information.

## Ethical and Legal Disclaimer

This repository documents an educational assessment. Scan only systems you own or are explicitly authorized to assess. The documentation is for defensive learning and does not include exploitation instructions. Nmap results are time-bound observations and must be validated by the system owner before operational decisions.
