# Cybersecurity Labs

Hands-on cybersecurity exercises, investigation notes, scan reports, and defensive recommendations.

## Projects

| Folder | What it contains | Start here |
| --- | --- | --- |
| [`Nmap/`](Nmap/README.md) | Authorized-lab network service and vulnerability-indicator assessment | [Nmap project README](Nmap/README.md) |
| [`brute-force-investigation/`](brute-force-investigation/README.md) | Windows Event Viewer investigation of repeated failed logins | [Investigation README](brute-force-investigation/README.md) |

Each project folder has its own README. Subfolders also include a README when they contain a distinct type of material, so you can navigate directly to the relevant report, findings, remediation notes, or evidence.

## Repository Guide

- Open a project README first for its purpose, scope, and contents.
- Use the Nmap [findings guide](Nmap/documentation/README.md) to navigate scan analysis.
- Use the Nmap [remediation guide](Nmap/remediation/README.md) for prioritized defensive actions and verification.
- The brute-force investigation's [`screenshots/`](brute-force-investigation/screenshots/README.md) folder describes its evidence.

## Authorization and Safety

All scanning and security testing must be limited to systems you own or are explicitly authorized to assess. Findings are educational observations, not proof of exploitability; verify them with the system owner before taking action. Do not publish credentials, private keys, tokens, or unnecessary personal or host-identifying information.
