# `host`: Quick DNS Lookups

The `host` command looks up DNS records for a domain or hostname. It is useful for quick checks when you want an answer without the full DNS response details.

## Install

On Kali Linux or Debian:

```bash
sudo apt update
sudo apt install bind9-dnsutils
```

## Basic usage

```bash
host example.com
```

This usually displays the domain's IPv4 address and any mail servers returned by DNS. The exact records can change over time.

Ask for one record type with `-t`:

```bash
host -t A example.com
host -t AAAA example.com
host -t MX example.com
host -t NS example.com
host -t TXT example.com
```

`A` is an IPv4 address, `AAAA` is an IPv6 address, `MX` lists mail servers, `NS` lists name servers, and `TXT` returns text records.

To look up a reverse DNS name for an IP address:

```bash
host 203.0.113.10
```

`203.0.113.10` is an example address reserved for documentation, so this example may not return a hostname.

## Choose a DNS server

You can ask a particular DNS server to answer the query:

```bash
host example.com 1.1.1.1
```

The server address goes after the domain. This can help compare answers from different resolvers.

## Zone-transfer check

An AXFR request asks an authoritative DNS server to return a zone's records. Use it only when you own the domain or have written permission to test it:

```bash
host -l <authorized-domain> <authoritative-name-server>
```

Replace both placeholders with values from your authorized lab. A response such as `REFUSED` means the server denied the request; it does not by itself indicate a security issue. A successful transfer can disclose DNS records and should be reported to the system owner.

## Common results

- `has address` or `mail is handled by`: DNS returned the requested record.
- `NXDOMAIN`: the queried name does not exist in DNS, or the name was typed incorrectly.
- `REFUSED`: the server understood the query but will not answer it.
- Timeout: the server did not answer in time; network conditions or filtering may be responsible.

DNS answers are observations, not proof that a service is reachable or vulnerable. Test only systems you own or are explicitly authorized to assess.
