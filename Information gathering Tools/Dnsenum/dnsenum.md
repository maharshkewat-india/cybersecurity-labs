# `dnsenum`: DNS Enumeration

`dnsenum` collects DNS information for a domain. Depending on its options and version, it can look up common records, try subdomain names from a wordlist, request zone transfers, and perform reverse lookups. These actions generate traffic, so use the tool only within an authorized assessment.

## Install

On Kali Linux or Debian:

```bash
sudo apt update
sudo apt install dnsenum
```

Check the installed version and available options with:

```bash
dnsenum --help
```

## Run a scoped enumeration

Use a domain from your own lab or an assessment scope that explicitly permits DNS enumeration:

```bash
dnsenum --noreverse --nocolor --timeout 3 --threads 2 <authorized-domain>
```

Replace `<authorized-domain>` with the permitted domain. The options above skip reverse lookups, disable terminal colors, set a three-second timeout, and limit concurrency to two threads. The tool may still try other enabled checks, including subdomain discovery and zone-transfer requests.

For less activity, query one DNS record with `host` or `dig` instead. See the [host guide](../Host/host.md) and [dig guide](../Dig/dig.md).

## Understand the sections

Names and headings vary slightly by `dnsenum` version. Common results include:

- **Host addresses:** address records (usually IPv4/A records) for the requested domain.
- **Name servers:** NS records that identify servers responsible for DNS answers.
- **Mail servers:** MX records and their priorities. A lower MX priority number is preferred.
- **Zone-transfer results:** whether a name server accepted an AXFR request. `REFUSED` means it denied the request; a successful transfer can expose many DNS records.
- **Brute-force results:** names found by checking words from a dictionary. A discovered subdomain is not, by itself, a vulnerability.
- **Reverse lookups:** PTR queries for IP addresses. These can be numerous and may take time, which is why `--noreverse` is useful for a smaller run.

DNS data can be stale, incomplete, or shared across hosting providers. Confirm important findings with the system owner and current authoritative data.

## Options to know

- `--noreverse`: skip reverse DNS lookups.
- `--threads <number>`: control concurrent queries; keep this low for a small lab run.
- `-t, --timeout <seconds>`: set DNS query timeouts.
- `--dnsserver <server>`: choose the DNS server used for A, NS, and MX queries.
- `-f <wordlist>`: choose a subdomain wordlist. Wordlist checks create additional queries.
- `--subfile <file>`: save discovered subdomains to a file.
- `--nocolor`: disable colored output, useful for logs.

Read `dnsenum --help` for options supported by your installed version. Avoid broad reverse lookups and large wordlists unless they are in the written scope.

## Safety

Only enumerate domains you own or have explicit permission to assess. Do not treat a zone-transfer denial as proof that every DNS setting is secure, and do not publish records that could expose internal or personal information.
