- [**SMB**](https://learn.microsoft.com/en-us/windows-server/storage/file-server/file-server-smb-overview) (**S**erver **M**essage **B**lock): a network file sharing protocol, used for files and peripherals sharing, in Windows.
	- Ports: `445` (TCP), `139` (NetBIOS).
	- Two levels of authentication to access a share:
		- *Users Authentication*: `username` & `password`.
		- Share Authentication: `password`.
		- Both utilize a challenge response authentication system.
- **SAMBA** is the open source Linux SMB.
	- Allows Windows systems to access Linux shares.

> [!INFO]
> All Windows systems have an account called Administrator.

## Authentication
![[Pasted image 20250216190733.png|center]]
1. Auth request from the client to the server.
2. The server request the client to encrypt string with user’s hash.
3. The client sends the encrypted strings to the server.
4. The server checks the actual string value of that users matches the client’s one, and grant access. It doesn’t match access is denied.

## PsExec

> [`psexec`](https://learn.microsoft.com/en-us/sysinternals/downloads/psexec): *a light-weight telnet-replacement that lets you execute processes on remote systems, complete with full interactivity for console applications, using any user's credentials.*

- PsExec authentication is performed via SMB
- Run arbitrary commands or a remote command prompt
- Commands are sent via `CMD` (without a GUI like `RDP`)
- Legitimate user account and passwords/hashes are necessary to gain Windows target access

### Exploitation
1. Leverage various techniques, e.g. **SMB login brute-force** attack.
	- Narrow down the attack to only common Win user accounts, e.g. **Administrator**.
2. Use the obtained credentials to authenticate via `**PsExec**` and execute system commands or get a reverse shell. 

## Tools

> [[SMBClient]]

> [[Enum4Linux]]

> Nmap

List the supported protocols and dialects of an SMB server:

```sh
nmap -p445 --script smb-protocols demo.ine.local
```

Run security mode script to return the information about the SMB security level:

```sh
nmap -p445 --script smb-security-mode demo.ine.local
```

Enumerate users logged in:

```sh
nmap -p445 --script smb-enum-sessions demo.ine.local
```

Previous will work if the guest login is enabled in the target machine. Otherwise, use valid credentials:

```sh
nmap -p445 --script smb-enum-sessions --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

Enmerate all available shares:

```sh
nmap -p445 --script smb-enum-shares demo.ine.local
```

Same but with valid credentials to check share permissions:

```sh
nmap -p445 --script smb-enum-shares --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

Enumerate windows users:

```sh
nmap -p445 --script smb-enum-users --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

Get information about server statistics:

```sh
nmap -p445 --script smb-server-stats --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

Enumerate available domains:

```sh
nmap -p445 --script smb-enum-domains --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

Enumerate available user groups:

```sh
nmap -p445 --script smb-enum-groups --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

Enumerate services:

```sh
nmap -p445 --script smb-enum-services --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

Enumerate all the shared folders and drives then running the `ls` command:

```sh
nmap -p445 --script smb-enum-shares,smb-ls --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

Find exact version of samba server (+ additional information)

```sh
nmap --script smb-os-discovery.nse -p 445 demo.ine.local
```

### Metasploit Modules

| Module                              | Description                                         |
| ----------------------------------- | --------------------------------------------------- |
| auxiliary/scanner/smb/smb_version   | SMB Version Detection                               |
| auxiliary/scanner/smb/smb_enumusers | SMB User Enumeration (SAM EnumUsers)                |
| auxiliary/scanner/smb/smb_login     | SMB Login Check Scanner                             |
| exploit/widnows/smb/psexec          | Microsoft Windows Authenticated User Code Execution |

## SMB Exploitation With PsExec
1. Identify legitimate user accounts and their respective passwords or password hashes. For that, we can bruteforce using the Metasploit `smb_login` module.
2. Run the script `psexec.py` (find the script with `locate psexec.py`):

```sh
psexec.py <user>@<ip> <command> # Administrator@10.2.24.221 cmd.exe
```

Alternatively, if we want a meterpreter sessions, we can use the Metasploit `smb/psexec` module.

## Vulnerabilities
### MS17-010 (EternalBlue)
- Collection of Windows vulnerabilities and exploits that allow attackers to remotely execute arbitrary code and gain access to a Windows system and consequently the network that the target system is part of.
- Takes advantage of a vulnerability in SMBv1 protocl that allow attackers to **send specially crafted packets that consequently facilitate the execution or arbitrary commands.**
- Affected versions:
	- Windows Vista
	- Windows 7
	- Windows Server 2008
	- Windows 8.1
	- Windows Server 2012
	- Windows 10
	- Windows Server 2016

#### Exploitation
- Nmap script: `smb-vuln-ms17-010`
- [AutoBlue](https://github.com/3ndG4me/AutoBlue-MS17-010)