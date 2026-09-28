# Remediation Guide

This plan is based only on the findings in the Nmap report for `10.0.2.3`. The host appears to be a deliberately vulnerable lab system; do not apply changes to a production system without an approved change plan and owner review.

## Immediate Actions

1. **Isolate the host pending review.** Nmap reported the vsftpd 2.3.4 backdoor and a check result showing `uid=0(root)`, and it flagged a suspected trojaned UnrealIRCd. Restrict network access while preserving evidence required by the lab or incident-response process.
2. **Remove or replace affected services from trusted sources.** Rebuild the host from a known-good image if compromise or tampering is confirmed or cannot be ruled out. Do not rely solely on restarting the daemon.
3. **Disable the root-shell listener on TCP/1524.** Nmap labeled this service `Metasploitable root shell`. Confirm it is absent after remediation.
4. **Disable unnecessary insecure/legacy listeners.** Review Telnet (23), rexecd (512), rlogin (513), FTP (21/2121), IRC (6667), and any unneeded remote administration or file-sharing services. Replace required remote access with supported, encrypted, access-controlled alternatives.
5. **Restrict exposure.** Limit database (3306/5432), RPC/NFS (111/2049), SMB (139/445), RMI (1099), VNC (5900), X11 (6000), AJP (8009), and web/admin endpoints to authorized hosts or networks. Close ports that have no approved business purpose.

## Short-Term Actions

- Upgrade or replace the reported legacy software using supported vendor packages: vsftpd, OpenSSH, BIND, Apache HTTP Server, ProFTPD, MySQL, PostgreSQL, UnrealIRCd, and Tomcat as applicable. The scan's version-based CVE matches are candidates for investigation, not a verified patch list.
- For Postfix and PostgreSQL TLS, disable SSLv3, anonymous DH, export-grade cipher suites, and weak DH groups. Apply vendor updates for the TLS libraries and verify that only approved modern protocol/cipher configurations remain.
- For the RMI registry, disable remote class loading and restrict registry access; disable the service if it is not needed.
- For Tomcat, apply supported updates and configure request/connection timeouts and resource limits relevant to the reported Slowloris indicator.
- Review port-80 and port-8180 findings: disable TRACE if unnecessary; inspect enumerated paths; remove diagnostic files and unused admin/upload endpoints; verify CSRF defenses; and set `HttpOnly` on session cookies where appropriate. Nmap described some paths/forms as possible and did not confirm exploitable CSRF.
- Review host logs and package provenance for unexpected changes, focusing on services Nmap flagged as backdoored or vulnerable.

## Long-Term Security Improvements

- Maintain an inventory of approved services, owners, supported versions, and network exposure.
- Establish a patch-management process that checks vendor advisories and backports rather than relying on banner-to-CVE matching alone.
- Enforce host and network firewall rules based on documented service dependencies; periodically remove unused listeners.
- Use a supported TLS baseline and test it after platform/library updates.
- Schedule authorized, non-destructive service discovery and configuration review; retain dated results so changes can be compared.
- Store original reports securely and publish only sanitized copies. The source report includes a MAC address and hostnames; remove details not required for the portfolio.

## Safe Verification

Run checks only against systems you own or are explicitly authorized to assess. These commands perform service/version or TLS cipher enumeration; they do not invoke the `vuln` script category or exploit checks.

Check whether selected TCP ports remain reachable and identify banners:

```sh
nmap -sT -sV --reason -p 21,22,23,25,53,80,111,139,445,512,513,514,1099,1524,2049,2121,3306,5432,5900,6000,6667,8009,8180 10.0.2.3
```

Review TLS protocols and cipher suites on the services where the report identified TLS findings:

```sh
nmap -sT -p 25,5432 --script ssl-enum-ciphers 10.0.2.3
```

Compare results with the approved service inventory and configuration. Confirm that disabled ports are filtered/closed as intended and that TLS no longer offers SSLv3, anonymous DH, export-grade ciphers, or weak DH groups. Validate package versions and patch status directly on the host through the operating system's trusted package-management tools; Nmap banner matching alone cannot prove remediation or CVE applicability.

Record the verification date, source, authorized scope, command, output, and remaining exceptions. Avoid publishing raw output that exposes unnecessary host details.