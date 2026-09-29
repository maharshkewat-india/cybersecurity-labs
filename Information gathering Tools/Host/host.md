# `host`: DNS lookup examples from lab output

The `host` command is a simple DNS utility used to query domains and display address, nameserver, and mail-server information. In the lab, it was used against `nptel.ac.in`, `iitkgp.ac.in`, and `zonetransfer.me`.

## 1. Help output

```bash
host -h
```

This does not show normal help output because `host` recognizes `-V` or a plain command without an option. The command actually prints usage when the invalid flag is used:

```text
host: illegal option -- h
Usage: host [-aCdilrTvVw] [-c class] [-N ndots] [-t type] [-W time]
```

This tells us the tool expects a domain name or a valid record type, not `-h` as help.

## 2. Basic A record lookup

```bash
host nptel.ac.in
```

Output:

```text
nptel.ac.in has address 8.233.49.88
nptel.ac.in mail is handled by 30 aspmx3.googlemail.com.
nptel.ac.in mail is handled by 20 alt1.aspmx.l.google.com.
nptel.ac.in mail is handled by 30 aspmx2.googlemail.com.
nptel.ac.in mail is handled by 10 aspmx.l.google.com.
nptel.ac.in mail is handled by 10 mailx1.iitm.ac.in.
nptel.ac.in mail is handled by 20 alt2.aspmx.l.google.com.
```

This shows:
- `A` record: `8.233.49.88`
- `MX` records: Google Mail servers and `mailx1.iitm.ac.in`

## 3. Name server lookup

```bash
host -t ns nptel.ac.in
```

Output:

```text
nptel.ac.in name server ns-cloud-a2.googledomains.com.
nptel.ac.in name server ns-cloud-a4.googledomains.com.
nptel.ac.in name server ns-cloud-a1.googledomains.com.
nptel.ac.in name server ns-cloud-a3.googledomains.com.
```

This confirms the authoritative nameservers used by the domain.

## 4. Zone transfer check

```bash
host -l nptel.ac.in ns-cloud-a3.googledomains.com
```

Output:

```text
Using domain server:
Name: ns-cloud-a3.googledomains.com
Address: 216.239.36.106#53
Aliases:

Host nptel.ac.in not found: 5(REFUSED)
; Transfer failed.
```

`REFUSED` means the DNS server did not allow the zone transfer. This is a normal defensive configuration for many production domains.

## 5. Query on another domain

```bash
host iitkgp.ac.in
```

Output:

```text
iitkgp.ac.in has address 203.110.243.180
iitkgp.ac.in mail is handled by 5 mx1.iitkgp.ac.in.
iitkgp.ac.in mail is handled by 5 mx2.iitkgp.ac.in.
```

This is a straightforward A/MX lookup, showing another domain's IP and mail servers.

## 6. Zone transfer test on `zonetransfer.me`

```bash
host zonetransfer.me
```

Output:

```text
zonetransfer.me has address 5.196.105.14
zonetransfer.me mail is handled by 10 ALT1.ASPMX.L.GOOGLE.COM.
zonetransfer.me mail is handled by 0 ASPMX.L.GOOGLE.COM.
zonetransfer.me mail is handled by 20 ASPMX2.GOOGLEMAIL.COM.
zonetransfer.me mail is handled by 20 ASPMX5.GOOGLEMAIL.COM.
zonetransfer.me mail is handled by 10 ALT2.ASPMX.L.GOOGLE.COM.
zonetransfer.me mail is handled by 20 ASPMX4.GOOGLEMAIL.COM.
zonetransfer.me mail is handled by 20 ASPMX3.GOOGLEMAIL.COM.
```

```bash
host -t ns zonetransfer.me
```

Output:

```text
zonetransfer.me name server nsztm2.digi.ninja.
zonetransfer.me name server nsztm1.digi.ninja.
```

```bash
host -l zonetransfer.me nsztm1.digi.ninja.
```

Output included many records such as:

```text
;; communications error to 81.4.108.41#53: timed out
...
zonetransfer.me has address 5.196.105.14
zonetransfer.me name server nsztm1.digi.ninja.
zonetransfer.me name server nsztm2.digi.ninja.
14.105.196.5.IN-ADDR.ARPA.zonetransfer.me domain name pointer www.zonetransfer.me.
asfdbbox.zonetransfer.me has address 127.0.0.1
canberra-office.zonetransfer.me has address 202.14.81.230
...
```

This demonstrates that a DNS zone transfer can expose internal-looking records and subdomains if the server is misconfigured. In a lab, this is a useful example of insecure DNS exposure.

## Key takeaway

`host` is useful for quick reconnaissance: A records, MX records, nameservers, and AXFR attempts. For the observed results, the key lesson is that `REFUSED` denies transfer, while a successful transfer can reveal a large amount of zone data.
