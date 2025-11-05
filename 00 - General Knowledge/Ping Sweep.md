
| ICMP | Request | Reply |
| ---- | ------- | ----- |
| Type | 8       | 0     |
| Code | 0       | 0     |

- **Type**: purpose or function of the ICMP message.
- **Code**: additional information or context related to the type.

If hosts does not answer:
- Host is offline, or
- network congestion, or
- temporary unavailability or
- firewall settings that block ICMP traffic.
Ping seeps won’t work very often against Windows systems, because it block ICMP requests by default. In these case (and generally…), we can use `nmap -pn`. See [[Nmap]].

## Tools
- [[Ping]]
- [[Fping]]
- [[Nmap]]