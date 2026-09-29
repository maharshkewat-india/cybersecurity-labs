# `dig`: DNS Queries and Answers

`dig` (Domain Information Groper) sends DNS queries and displays the response sections. Use it when you need to inspect a particular record type or understand more than a short lookup result.

## Install

On Kali Linux or Debian:

```bash
sudo apt update
sudo apt install bind9-dnsutils
```

## Basic usage

```bash
dig example.com A
```

The final argument is the record type. `A` requests an IPv4 address. The answer may change over time; example output in this guide uses documentation-only values.

Common record queries:

```bash
dig example.com AAAA
dig example.com MX
dig example.com NS
dig example.com TXT
```

Use `+short` to print only the answer values:

```bash
dig example.com A +short
```

## Read the response

In the full response, start with `status` and `ANSWER SECTION`:

```text
;; ->>HEADER<<- opcode: QUERY, status: NOERROR
;; ANSWER SECTION:
example.com.  300  IN  A  203.0.113.10
```

`NOERROR` means the DNS query completed successfully; it does not guarantee that a web server is available. In an answer row, the fields show the queried name, cache lifetime (TTL), record class, type, and value. `203.0.113.10` is reserved for documentation and is not a real target.

Other response statuses include `NXDOMAIN` (the name does not exist) and `SERVFAIL` (the server could not complete the lookup). An empty answer can also mean that the requested record type is not configured.

## Ask a specific resolver

```bash
dig @1.1.1.1 example.com A
```

The `@` prefix selects the DNS resolver. Without it, `dig` uses the resolver configured on your machine.

## Reverse lookup

```bash
dig -x 203.0.113.10 +short
```

`-x` requests a PTR record for an IP address. Reverse DNS records are optional, so no answer does not necessarily mean there is a problem.

## Zone-transfer check

AXFR requests can disclose a domain's DNS zone when a server allows them. Run this only against a domain and name server that you own or have written permission to test:

```bash
dig @<authoritative-name-server> <authorized-domain> AXFR
```

Replace both placeholders with authorized lab values. A transfer containing many records indicates that the server allowed the request; coordinate with the owner before drawing conclusions or sharing the data. `REFUSED` means the server denied the request.

## Useful options

- `+short`: show answer values only.
- `+noall +answer`: show only the answer section.
- `+trace`: follow DNS delegation from the root servers; this generates multiple queries.
- `-t MX`: choose a record type (equivalent to placing `MX` after the domain).

DNS lookups are one part of reconnaissance, not a vulnerability verdict. Limit testing to systems you own or are explicitly authorized to assess.
