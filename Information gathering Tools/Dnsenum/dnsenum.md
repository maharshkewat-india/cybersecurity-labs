# `dnsenum`: DNS enumeration examples from lab output

`dnsenum` is a DNS reconnaissance utility that can gather host addresses, nameservers, mail servers, and subdomain data. In the lab it was used against `nptel.ac.in` and `zonetransfer.me`.

## 1. Help output

```bash
dnsenum -h
```

This shows the main options, including:
- `--dnsserver`
- `--enum`
- `--noreverse`
- `--threads`
- `-f, --file`
- `--whois`
- `-o --output`

A key part of the output is that `dnsenum` can perform a zone transfer and brute-force subdomain discovery.

## 2. Query against `nptel.ac.in`

```bash
dnsenum nptel.ac.in
```

The result included:

```text
Host's addresses:
nptel.ac.in.                             1        IN    A        8.233.49.88

Name Servers:
ns-cloud-a1.googledomains.com.           39604    IN    A        216.239.32.106
ns-cloud-a3.googledomains.com.           7307     IN    A        216.239.36.106
ns-cloud-a4.googledomains.com.           11178    IN    A        216.239.38.106
ns-cloud-a2.googledomains.com.           39596    IN    A        216.239.34.106

Mail (MX) Servers:
aspmx3.googlemail.com.                   1        IN    A        108.177.125.26
mailx1.iitm.ac.in.                       86400    IN    A        103.158.42.50
...
```

The DNS transfer attempt then showed:

```text
Trying Zone Transfer for nptel.ac.in on ns-cloud-a1.googledomains.com ...
AXFR record query failed: REFUSED
```

The same result repeated for all nameservers, showing the domain is not permitting zone transfer.

The brute-force section then reported possible subdomains such as:

```text
archive.nptel.ac.in.                     454      IN    A        8.233.49.88
beta.nptel.ac.in.                        1        IN    A        8.232.69.236
dev.nptel.ac.in.                         7200     IN    A        103.158.43.163
forums.nptel.ac.in.                      7200     IN    A        14.139.160.155
staging.nptel.ac.in.                     300      IN    A        34.93.59.74
www.nptel.ac.in.                         3317     IN    CNAME    nptel.ac.in.
```

These are important enumeration findings, but they are not proof of vulnerability by themselves.

## 3. Query against `zonetransfer.me`

```bash
dnsenum zonetransfer.me
```

This produced a much more revealing result:

```text
Host's addresses:
zonetransfer.me.                         1        IN    A        5.196.105.14

Name Servers:
nsztm1.digi.ninja.                       8564     IN    A        81.4.108.41
nsztm2.digi.ninja.                       8564     IN    A        5.196.105.10
```

The zone transfer section then produced a large amount of DNS data:

```text
Trying Zone Transfer for zonetransfer.me on nsztm1.digi.ninja ...
zonetransfer.me.                         7200     IN    SOA               (
zonetransfer.me.                         7200     IN    DNSKEY            (
zonetransfer.me.                         301      IN    TXT               (
zonetransfer.me.                         7200     IN    MX                0
...
14.105.196.5.IN-ADDR.ARPA.zonetransfer.me. 7200     IN    PTR      www.zonetransfer.me.
canberra-office.zonetransfer.me.         7200     IN    A        202.14.81.230
home.zonetransfer.me.                    7200     IN    A        127.0.0.1
office.zonetransfer.me.                  7200     IN    A        4.23.39.254
vpn.zonetransfer.me.                     4000     IN    A        174.36.59.154
xss.zonetransfer.me.                     300      IN    TXT      "'><script>alert('Boo')</script>"
```

This is a clear example of an exposed DNS zone. The data included internal-looking hosts, a PTR record, a TXT record, and other details that would normally be kept private.

## 4. Interpretation

The lab findings show two patterns:

- `nptel.ac.in` refused AXFR, so basic reconnaissance succeeded but no transfer was allowed.
- `zonetransfer.me` allowed zone transfer, exposing a large set of DNS records.

This is why `dnsenum` is useful for DNS reconnaissance, but it must be used only against authorized targets.
