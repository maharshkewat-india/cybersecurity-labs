# Information Gathering Tools

This section contains beginner-friendly notes for three DNS tools. Each guide includes common commands, an explanation of the results, and reminders about safe testing.

| Tool | Use it for | Guide |
| --- | --- | --- |
| `host` | Quick DNS lookups | [host guide](Host/host.md) |
| `dig` | Detailed DNS queries and response inspection | [dig guide](Dig/dig.md) |
| `dnsenum` | Authorized DNS and subdomain enumeration | [dnsenum guide](Dnsenum/dnsenum.md) |

## Before you test

DNS queries can reveal information about a domain. Enumeration tools may also send many requests or attempt a zone transfer. Test only domains you own or have explicit permission to assess, and follow the assessment scope. Examples in these guides use reserved documentation IP addresses where an address is needed.

These notes are educational references. DNS results change over time and should not be treated as proof that a service is reachable or vulnerable.
