[**RDP**](https://learn.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol): **R**emote **D**esktop **P**rotocol:  Microsoft proprietary GUI remote access protocol used to remotely connect with Windows.
- Ports: `3389` (TCP) by default.
- *Requires User Authentication* - `username`& `password`
- An RDP client is used to connect to the target.

Brute-force with `hydra`:
```sh
hydra -L /usr/share/metasploit-framework/data/wordlists/common_sers.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt rdp://<host> -s <port>
```

> [!WARNING]
> Hydra uses 16 threads by default. It could cause a DoS. We can change the number of threads with `-t`. See hydra help with `hydra -h`

Connect using xfreerdp
```sh
xfreerdp /u:<user> /p:<password> /v:<host[:port]> # For fullscreen /f
```
# Metasploit modules
| Module                            | Description                                                   |
| --------------------------------- | ------------------------------------------------------------- |
| auxiliary/scanner/rdp/rdp_scanner | Identify endpoints speaking the Remote Desktop Protocol (RPD) |

# Vulnerabilities
## BlueKeep (CVE-2019-0708)
- Vulnerability found in RDP protocol that allows attackers to **gain access to a chunk of kernel memory** consequently allowing them to **remotely execute arbitrary code** at the system level without authentication.

> [!NOTE]
> Targeting Kernel space memory and applications can cause system crashes.

### Exploitation
#### Metasploit modules
| Module                                         | Description                                                     |
| ---------------------------------------------- | --------------------------------------------------------------- |
| auxiliary/scanner/cve_2019_0708_bluekeep       | CVE-20190708 BlueKeep Microsoft Remote Desktop RCE Check        |
| exploit/windows/rdp/cve_2019_0708_bluekeep_rce | CVE-2019-0708 BlueKeep RDP Remote Windows Kernel Use After Free |
