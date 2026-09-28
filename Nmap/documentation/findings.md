# Assessment Findings

## Scope and Interpretation

The source report is an Nmap 7.99 scan of `10.0.2.3`, initiated September 26, 2026. The recorded command used TCP service/version detection and NSE scripts in the `vuln` category. Nmap reported one host up, `0.00068s` latency, 977 closed TCP ports (reset), and the 23 open TCP ports listed in the README.

This document distinguishes:

- **Direct Nmap finding:** a port, service banner, script output, or explicit script state in the report.
- **Version-indexed reference:** an entry returned by the `vulners` script for a detected CPE/version. A match alone does not establish that a CVE applies to the installed build or configuration.
- **Interpretation:** plain-language context for a finding, not additional scan evidence.
- **Recommendation:** defensive action, not a claim about the host's current configuration.

The scan command, environment table, and full port inventory are in the [README](../../README.md#lab-environment). The workspace source report is `Nmap/namp_scan.txt`.

## Confirmed or Explicit NSE Indicators

### Port 21 — FTP (vsftpd 2.3.4)

**Service information:** TCP/21; FTP; `vsftpd 2.3.4`.

**Nmap finding:** `ftp-vsftpd-backdoor` reported `VULNERABLE (Exploitable)` for the vsftpd 2.3.4 backdoor and associated it with `BID:48539` and **CVE-2011-2523**. The script output includes a check result showing `uid=0(root)`.

**Interpretation and impact:** Nmap's script result indicates the backdoored build may permit commands to run with root privileges. Treat the host as potentially compromised; the scan result is not a benign version-only match.

**CVE / score:** CVE-2011-2523. The separate `vulners` output associates a score of `10.0` with this CVE; the `ftp-vsftpd-backdoor` output itself does not state a CVSS score.

**Defensive action:** Isolate the host from untrusted networks, preserve evidence as required, remove the affected package, and rebuild from a trusted image rather than relying only on an in-place upgrade. Review accounts, processes, files, and logs for unauthorized changes. Do not expose FTP unless a documented business need remains.

### Port 25 — SMTP (Postfix smtpd), TLS indicators

**Service information:** TCP/25; SMTP; `Postfix smtpd`. No Postfix version is specified in the scan report.

**Nmap findings:**

- `ssl-dh-params` reported **Anonymous Diffie-Hellman Key Exchange MitM Vulnerability**. The check identified `TLS_DH_anon_WITH_3DES_EDE_CBC_SHA` and a 1024-bit modulus.
- The same script reported **DHE_EXPORT ciphers downgrade MitM (Logjam)** as `VULNERABLE`, with **CVE-2015-4000**. The check identified `TLS_DHE_RSA_EXPORT_WITH_DES40_CBC_SHA` and a 512-bit modulus.
- It reported **Diffie-Hellman Key Exchange Insufficient Group Strength** as `VULNERABLE`, identifying a 1024-bit group and `TLS_DHE_RSA_WITH_AES_256_CBC_SHA`.
- `ssl-poodle` reported **SSL POODLE information leak** as `VULNERABLE`, with **CVE-2014-3566** and the check result `TLS_RSA_WITH_AES_128_CBC_SHA`.
- `sslv2-drown` failed to execute. This is a script error, not a positive DROWN finding.
- `smtp-vuln-cve2010-4344` reported that the server is not Exim and therefore `NOT VULNERABLE` to that Exim-specific check.

**Interpretation and impact:** Anonymous key exchange lacks peer authentication; export-grade and weak DH parameters weaken TLS protection; and SSLv3 POODLE is a legacy protocol/cipher concern. Depending on exposure and negotiation, these conditions can undermine confidentiality or integrity. The report does not provide a numeric severity score for these findings.

**CVE / reference:** CVE-2015-4000 (Logjam); CVE-2014-3566 (POODLE). No CVE is listed for the anonymous-DH or weak-group output in this section of the report.

**Defensive action:** Disable anonymous and export-grade cipher suites, reject SSLv3, use maintained TLS versions and strong, appropriately sized DH parameters or modern approved key exchange, and restrict SMTP access to required peers. Confirm the effective Postfix TLS configuration after changes.

### Port 1099 — Java RMI (GNU Classpath grmiregistry)

**Service information:** TCP/1099; Java RMI; `GNU Classpath grmiregistry`.

**Nmap finding:** `rmi-vuln-classloader` reported `VULNERABLE`: its description says the default RMI registry configuration allows loading classes from remote URLs, which can lead to remote code execution. No CVE or score is supplied by this finding.

**Interpretation and impact:** If the reported behavior is present and reachable by an untrusted party, it may allow execution of code in the service's security context. The report does not establish the service account's privileges.

**CVE / reference:** No CVE specified in this finding. Nmap provides a reference to the Rapid7 RMI module; this documentation does not reproduce or use it.

**Defensive action:** Disable RMI if unnecessary. Otherwise upgrade to a supported implementation, disable remote code downloading, bind to trusted interfaces, and firewall access to authorized management hosts.

### Port 5432 — PostgreSQL (PostgreSQL DB 8.3.0 - 8.3.7), TLS indicators

**Service information:** TCP/5432; PostgreSQL; `PostgreSQL DB 8.3.0 - 8.3.7`.

**Nmap findings:**

- `ssl-poodle` reported **SSL POODLE information leak** as `VULNERABLE`, associated with **CVE-2014-3566**.
- `ssl-dh-params` reported **Diffie-Hellman Key Exchange Insufficient Group Strength** as `VULNERABLE`, identifying a 1024-bit group.
- `ssl-ccs-injection` reported **SSL/TLS MITM vulnerability (CCS Injection)** as `VULNERABLE` and gave `Risk factor: High`. Its references include **CVE-2014-0224**. The report's output describes affected OpenSSL version ranges but does not independently identify the installed OpenSSL package version.

**Interpretation and impact:** These script results indicate that TLS negotiation may permit legacy or weak cryptographic behavior. The scan does not prove that every client/server connection negotiates the affected modes, nor does it identify the exact OpenSSL build.

**CVE / score:** CVE-2014-3566 and CVE-2014-0224 are explicitly referenced. The CCS Injection script reports `High`; no numeric score is shown for these checks.

**Defensive action:** Upgrade PostgreSQL and its TLS library through supported vendor packages; disable SSLv3 and weak DH groups; use modern TLS configuration; and restrict database access to approved application and administration networks.

### Port 6667 — IRC (UnrealIRCd)

**Service information:** TCP/6667; IRC; `UnrealIRCd`.

**Nmap finding:** `irc-unrealircd-backdoor` reported: “Looks like trojaned version of unrealircd.” The script output provides no CVE or numeric severity.

**Interpretation and impact:** This is a suspected backdoored or tampered service indicator, not a version-indexed CVE match. Treat the host as untrusted until the package and system integrity are checked.

**CVE / score:** None specified in this finding.

**Defensive action:** Isolate the host, preserve relevant evidence, verify package provenance, and rebuild from trusted media if tampering is confirmed or cannot be ruled out. Disable IRC if it is not required.

### Port 8180 — HTTP (Apache Tomcat/Coyote JSP engine 1.1)

**Service information:** TCP/8180; HTTP; `Apache Tomcat/Coyote JSP engine 1.1`; Nmap reported the HTTP server header `Apache-Coyote/1.1`.

**Nmap finding:** `http-slowloris-check` reported `LIKELY VULNERABLE` to a Slowloris denial-of-service condition and associated the indicator with **CVE-2007-6750**. This is a likely result, not the script's `VULNERABLE` state.

**Interpretation and impact:** The described condition can allow a client to hold HTTP connections open and consume server resources, potentially reducing availability. No numeric severity score is reported.

**CVE / score:** CVE-2007-6750; the Slowloris check gives no numeric score. The separate Apache HTTP `vulners` output lists CVE-2007-6750 at `5.0` as a version-indexed reference for the Apache service on port 80, not as a score for the Tomcat check on port 8180.

**Defensive action:** Upgrade to a supported Tomcat release, apply vendor updates, configure connection and request timeouts and resource limits, and place the service behind appropriately configured network controls.

## TLS/SSL Findings

| Finding | Port(s) | Nmap-reported detail | CVE / severity in report |
| --- | --- | --- | --- |
| Anonymous Diffie-Hellman | 25 | `TLS_DH_anon_WITH_3DES_EDE_CBC_SHA`; 1024-bit modulus | No CVE or score specified |
| Weak Diffie-Hellman group | 25, 5432 | 1024-bit DH group reported | No CVE or score specified |
| Logjam / DHE_EXPORT downgrade | 25 | Export-grade 512-bit DH; `TLS_DHE_RSA_EXPORT_WITH_DES40_CBC_SHA` | CVE-2015-4000; no score specified |
| SSL POODLE | 25, 5432 | SSL POODLE check state `VULNERABLE` | CVE-2014-3566; no score specified |
| CCS Injection | 5432 | Script state `VULNERABLE`; risk factor `High` | CVE-2014-0224 in references; no numeric score specified |
| DROWN check | 25 | `sslv2-drown` script execution failed | No positive finding; no CVE asserted |

Nmap's cipher and protocol checks are evidence of what the endpoint negotiated or exposed to that scan. They do not provide a complete assessment of every client path or the software package versions.

## Web Observations (Port 80 and Port 8180)

These are script observations, not all confirmed vulnerabilities:

- On port 80, `http-trace` reported TRACE enabled.
- `http-csrf` listed possible forms at `/dvwa/` and on a TWiki documentation page. “Possible” is the script's wording; the output does not confirm exploitable CSRF.
- `http-enum` listed paths including `/phpinfo.php`, `/phpMyAdmin/`, `/doc/`, `/icons/`, `/index/`, and `/tikiwiki/` as possible or potentially interesting. The output is path enumeration, not proof that each path exposes sensitive data.
- On port 8180, `http-enum` listed possible administrative and upload-related paths; the Tomcat manager paths returned `401 Unauthorized` in the report.
- `http-cookie-flags` reported that `JSESSIONID` did not have the `HttpOnly` flag on listed port-8180 paths.
- The scan reported that it did not find stored or DOM-based XSS in the tested paths. SQL injection and one HTTP CVE script reported execution errors; errors are not negative or positive vulnerability results.

Review application routes and controls in an authorized test environment, remove unnecessary diagnostic/admin content, disable TRACE if not needed, and set appropriate session-cookie flags. Confirm behavior with the application owner before treating scanner heuristics as defects.

## Other Exposed Services

Nmap also detected Telnet (23), rpcbind/NFS (111/2049), Samba (139/445), rexecd (512), rlogin (513), a bindshell labeled `Metasploitable root shell` (1524), ProFTPD (2121), MySQL (3306), VNC (5900), X11 (6000; access denied), and AJP (8009). These are direct exposure findings, not all vulnerability confirmations. The `vulners` script returned version-indexed references for SSH, BIND, Apache HTTP Server, ProFTPD, MySQL, and PostgreSQL; see the qualification below.

The host script reported `smb-vuln-ms10-061: false` and `smb-vuln-ms10-054: false`. The SMB registry DoS script errored. These exact script outputs should not be generalized to unrelated SMB vulnerabilities.

## Service-by-Service Risk Explanation

The service descriptions below are general context; the concern column is limited to the observed exposure or an explicit scan result. An open port alone does not prove unauthorized access or a vulnerability.

| Service / port | What it does | Security concern from this report | Recommended action |
| --- | --- | --- | --- |
| FTP / TCP 21 | Transfers files | Nmap explicitly reported the vsftpd 2.3.4 backdoor as vulnerable; script output showed `uid=0(root)` | Isolate and rebuild from trusted media; remove or replace the affected service |
| SSH / TCP 22 | Encrypted remote administration | Old OpenSSH banner; `vulners` produced version-based matches, not confirmed CVEs | Upgrade to a supported package and restrict access; validate candidate CVEs against the installed vendor build |
| Telnet / TCP 23 | Remote terminal access | Telnet listener is exposed; the report does not assess its authentication configuration | Disable if unnecessary; use supported encrypted remote administration |
| SMTP / TCP 25 | Mail transfer | Nmap reported anonymous/weak DH, Logjam, and POODLE TLS indicators | Disable affected protocols/ciphers, update TLS components, and restrict peers |
| DNS / TCP 53 | DNS name service | ISC BIND 9.4.2 banner; `vulners` matches are not independently confirmed | Upgrade to a supported vendor package and validate version-indexed CVE candidates |
| HTTP / TCP 80 | Web content and applications | TRACE enabled; possible CSRF forms and enumerated paths were reported, not confirmed exploitable | Review application paths and controls; remove unneeded content and disable TRACE if not needed |
| rpcbind / TCP 111 | RPC service discovery | RPC services were enumerated, including NFS, mountd, nlockmgr, and status | Restrict RPC to required hosts and disable unneeded services |
| Samba / TCP 139 | NetBIOS/SMB file and printer sharing | Service exposed; two named SMB checks returned `false`, while another script errored | Restrict to authorized networks and review configuration; do not infer broader SMB safety from those checks |
| Samba / TCP 445 | SMB file and printer sharing | Service exposed; the report does not establish share permissions | Restrict access and review shares and permissions on the host |
| rexecd / TCP 512 | Legacy remote command service | `netkit-rsh rexecd` is exposed | Disable if unnecessary and use supported encrypted administration |
| rlogin / TCP 513 | Legacy remote login service | `rlogind` is exposed | Disable if unnecessary and use supported encrypted administration |
| tcpwrapped / TCP 514 | Service not identified | Port is open but Nmap did not identify a service behind the wrapper | Identify the owning process locally and close or restrict if unneeded |
| Java RMI / TCP 1099 | Java remote object registry | NSE reported vulnerable default remote class loading that can lead to code execution | Disable remote class loading, restrict registry access, or disable the service |
| bindshell / TCP 1524 | Nmap labeled it `Metasploitable root shell` | A root-shell service is exposed | Isolate the host and remove the listener; rebuild if host integrity is uncertain |
| NFS / TCP 2049 | Network file sharing | NFS is exposed; export permissions were not reported | Restrict to required clients and review export rules on the host |
| FTP / TCP 2121 | Transfers files | ProFTPD 1.3.1 banner; `vulners` returned version-indexed matches only | Upgrade to a supported package or disable; validate candidate CVEs |
| MySQL / TCP 3306 | Relational database service | Database listener exposed; scan did not test account permissions | Restrict to application/admin hosts and upgrade to a supported package |
| PostgreSQL / TCP 5432 | Relational database service | Database exposed; NSE also reported TLS POODLE, weak DH, and CCS Injection indicators | Restrict access, update software/TLS library, and harden TLS |
| VNC / TCP 5900 | Remote desktop access | VNC using protocol 3.3 is exposed; authentication state is not specified | Restrict to authorized management networks and disable if unnecessary |
| X11 / TCP 6000 | X Window System display service | X11 detected; the scan reported access denied | Keep restricted to trusted hosts and disable network access if not required |
| IRC / TCP 6667 | Internet Relay Chat service | NSE said the UnrealIRCd version looked trojaned | Isolate, verify package integrity, and rebuild from trusted media if needed |
| AJP / TCP 8009 | Apache JServ Protocol connector | AJP listener exposed; no vulnerability was confirmed by this scan | Restrict to required application peers or disable if unused |
| HTTP / TCP 8180 | Tomcat web application service | Slowloris check reported `LIKELY VULNERABLE`; possible admin paths were enumerated | Upgrade, apply request/connection limits, and restrict administration paths |

## Version-Indexed CVE References (Not Confirmed)

The scan's `vulners` output attaches CVE IDs and scores to detected CPE/version strings. For example, it lists CVE-2023-38408 at `9.8` under OpenSSH, CVE-2008-0122 at `10.0` under ISC BIND, CVE-2010-0425 at `10.0` under Apache HTTP Server, CVE-2019-12815 at `9.8` under ProFTPD, CVE-2017-15945 at `7.8` under MySQL, and CVE-2013-1903 at `10.0` under PostgreSQL. These IDs and values are reproduced as scanner output examples, not findings confirmed by the corresponding NSE vulnerability checks.

The report contains many more version-indexed entries. It also lists CVE-2007-6750 at `5.0` under the Apache HTTP service on port 80; this is separate from the `LIKELY VULNERABLE` Slowloris result on port 8180. Check the complete original output and validate each candidate against the exact vendor package, build, backports, and configuration before reporting applicability or prioritizing a CVE. No severity is assigned in this write-up beyond severity/score text explicitly printed by Nmap.

## Important Vulnerabilities Summary

| Vulnerability / indicator | Affected service | CVE / reference | Severity or score reported | Security impact (interpretation) |
| --- | --- | --- | --- | --- |
| vsftpd 2.3.4 backdoor; NSE state `VULNERABLE (Exploitable)` | FTP, TCP/21 | CVE-2011-2523 | `10.0` in `vulners` output; not in backdoor script output | Potential root-level command execution; Nmap's script check output showed `uid=0(root)` |
| Anonymous and weak DH; Logjam | SMTP/Postfix TLS, TCP/25 | CVE-2015-4000 for Logjam | No severity/score stated for direct script findings | Potentially weakened TLS confidentiality/integrity |
| SSL POODLE | SMTP/Postfix TLS, TCP/25; PostgreSQL TLS, TCP/5432 | CVE-2014-3566 | No score stated | Legacy SSLv3/cipher negotiation may expose protected data |
| RMI registry remote class-loading indicator | Java RMI, TCP/1099 | None stated | No severity/score stated | Could permit code execution under the service account if confirmed |
| PostgreSQL TLS CCS Injection | PostgreSQL TLS, TCP/5432 | CVE-2014-0224 in references | `High` risk factor; no numeric score | Potential TLS session compromise under affected conditions |
| Suspected trojaned UnrealIRCd | IRC, TCP/6667 | None stated | No severity/score stated | Possible service tampering/backdoor; verify package integrity |
| Slowloris | Tomcat HTTP, TCP/8180 | CVE-2007-6750 | `LIKELY VULNERABLE`; no numeric score for this check | Potential resource exhaustion and service unavailability |

Scores in the version-indexed `vulners` output are not represented as confirmation that a specific CVE applies.