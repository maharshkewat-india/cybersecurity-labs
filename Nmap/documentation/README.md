# Nmap Documentation

This folder contains the written analysis of the Nmap scan.

## Files

- [`findings.md`](findings.md) contains the port/service observations, explicit NSE results, TLS/SSL findings, service-by-service risk explanations, and version-indexed CVE caveats.

## Reading the Findings

The findings separate direct Nmap output from interpretation and defensive recommendations. Entries from the `vulners` script are version-based references and do not independently confirm that a CVE applies to the installed package. Check the exact vendor build, backports, and configuration before treating a match as applicable.

For the project overview and scan summary, return to the [Nmap README](../README.md). For mitigation steps, see the [remediation folder guide](../remediation/README.md).

Only publish reviewed and sanitized scan evidence. The original report can include host-identifying data that is unnecessary in a public portfolio.