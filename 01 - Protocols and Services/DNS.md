- Resolve domain names or hostnames to IP addresses.
- Humans do not learn IPs, learn names…

# Records
- **A**: ResolvesHostname or domain to IPv4 address.
- **AAAA**: Hostname or domain to IPv6 addresses.
- **NS**: Reference to the domain’s nameserver.
- **MX**: Resolves a domain to a mail server.
- **CNAME**: Aliases.
- **TXT**: Text record.
- **HINFO**: Host information.
- **SOA**: Domain authority.
- **SRV**: Service records.
- **PTR**: Resolves an IP address to a hostname.

The OS first uses */etc/hosts* to resolve domains.

The problem when doing DNS zone transfers (for example using [[dnsenum]]), is that attackers may see domain records to devices in the internal network that should not be exposed to the internet by the DNS server.