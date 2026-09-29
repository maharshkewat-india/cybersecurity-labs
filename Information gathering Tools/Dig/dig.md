# `dig`: DNS query examples from lab output

`dig` is a more detailed DNS query tool than `host`. It shows the request, response status, answer section, and query metadata. In the lab, `dig` was used to inspect A, NS, MX, and AXFR results.

## 1. Help output

```bash
dig -h
```

This prints `dig` usage and available options, including record types and query modifiers.

## 2. A record query for `nptel.ac.in`

```bash
dig nptel.ac.in
```

Key output:

```text
; <<>> DiG 9.20.27-2-Debian <<>> nptel.ac.in
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; ANSWER SECTION:
nptel.ac.in.            1       IN      A       8.233.49.88
```

This confirms:
- the query succeeded (`NOERROR`)
- the A record for `nptel.ac.in` is `8.233.49.88`

## 3. Short output

```bash
dig nptel.ac.in +short
```

Output:

```text
8.233.49.88
```

This is useful when only the value is needed, without the full DNS response header.

## 4. NS record query

```bash
dig nptel.ac.in -t ns
```

Output includes:

```text
;; ANSWER SECTION:
nptel.ac.in.            21600   IN      NS      ns-cloud-a4.googledomains.com.
nptel.ac.in.            21600   IN      NS      ns-cloud-a3.googledomains.com.
nptel.ac.in.            21600   IN      NS      ns-cloud-a2.googledomains.com.
nptel.ac.in.            21600   IN      NS      ns-cloud-a1.googledomains.com.
```

This shows the four nameservers delegated for the domain.

```bash
dig nptel.ac.in -t ns +short
```

Output:

```text
ns-cloud-a4.googledomains.com.
ns-cloud-a1.googledomains.com.
ns-cloud-a2.googledomains.com.
ns-cloud-a3.googledomains.com.
```

## 5. MX record query

```bash
dig nptel.ac.in -t mx
```

The output showed six MX records:

```text
nptel.ac.in.            7200    IN      MX      20 alt2.aspmx.l.google.com.
nptel.ac.in.            7200    IN      MX      10 aspmx.l.google.com.
nptel.ac.in.            7200    IN      MX      30 aspmx3.googlemail.com.
nptel.ac.in.            7200    IN      MX      30 aspmx2.googlemail.com.
nptel.ac.in.            7200    IN      MX      10 mailx1.iitm.ac.in.
nptel.ac.in.            7200    IN      MX      20 alt1.aspmx.l.google.com.
```

This helps identify the mail servers and priority values: lower preference numbers are preferred.

## 6. AXFR zone transfer against `zonetransfer.me`

```bash
dig axfr zonetransfer.me @nsztm1.digi.ninja
```

This returned a full zone transfer. Key output:

```text
zonetransfer.me.        7200    IN      SOA     nsztm1.digi.ninja. robin.digi.ninja. 2019100801 172800 900 1209600 3600
zonetransfer.me.        7200    IN      NS      nsztm1.digi.ninja.
zonetransfer.me.        7200    IN      NS      nsztm2.digi.ninja.
zonetransfer.me.        7200    IN      A       5.196.105.14
zonetransfer.me.        301     IN      TXT     "google-site-verification=..."
...
```

The response included many records, such as `MX`, `TXT`, `PTR`, `AFSDB`, and internal hostnames like:

```text
canberra-office.zonetransfer.me.         7200     IN     A      202.14.81.230
home.zonetransfer.me.                     7200     IN     A      127.0.0.1
vpn.zonetransfer.me.                      4000     IN     A      174.36.59.154
xss.zonetransfer.me.                      300      IN     TXT    "'><script>alert('Boo')</script>"
```

This shows why AXFR is considered a major DNS exposure when a server allows it.

## 7. Nameserver lookup for `zonetransfer.me`

```bash
dig zonetransfer.me -t ns
```

Output:

```text
zonetransfer.me.        1       IN      NS      nsztm1.digi.ninja.
zonetransfer.me.        1       IN      NS      nsztm2.digi.ninja.
```

## Key takeaway

`dig` is valuable because it exposes:
- the query status (`NOERROR`, `REFUSED`, etc.)
- the full answer section
- record types such as `A`, `NS`, and `MX`
- dangerous misconfigurations like successful `AXFR` transfers

In this lab, `nptel.ac.in` responded normally while `zonetransfer.me` showed a zone transfer that exposed lots of DNS data.
