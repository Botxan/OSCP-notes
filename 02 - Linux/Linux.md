## Frequently Exploited Services

| Protocol/Service                      | Ports            | Purpose                                                                                                                                                                         |
| ------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Apache Web Server                     | TCP ports 80/443 | Free and open source cross-platform web server released under the Apache License 2.0. Apache accounts for over 80% of web servers globally.                                     |
| [[SSH\|SSH (Secure Shell)]]           | TCP ports 22     | SSH is a cryptographic remote access protocol that is used to remotely access and control systems over an unsecured network. SSH was developed as a secure successor to telnet. |
| [[FTP\|FTP (File Transfer Protocol)]] | TCP port 21      | FTP (File Transfer Protocol) is a protocol that uses TCP port 21 and is used to facilitate file sharing between a server and client/clients and vice versa.                     |
| [[⚙ SMB - PsExec\|SAMBA]]                        | TCP port 445     | Samba is the Linux implementation of SMB, and allows Windows systems to access Linux shares and devices.                                                                        |

## Vulnerabilities
### CVE6271 (Shellshock)
Is a family of vulnerabilities in Bash shell (since V1.3) that allow an attacker to execute **arbitrary commands via Bash**, consequently allowing the attacker to **obtain remote access to the target system** via a reverse shell.

The vulnerability is caused because Bash mistakenly executes trailing commands after a series of characters: () {:;};.

In the context of remote exploitation, Apache web servers configured to run CGI scripts or `.sh` scripts are also vulnerable to this attack.
- CGI (Common Gateway Interface) scripts are used by Apache to execute arbitrary commands on the Linux system, after which the output is displayed to the client.

#### Exploit
Check if target is vulnerable with nmap:

```sh
nmap -sV <ip> --script=http-shellshock --script-args "http-shellshock.uri=/gettime.cgi"
```

##### nmap and burpsuite
Exploit with burpsuite:
![[Pasted image 20250209183328.png|center]]

Spawn a reverse shell:
![[Pasted image 20250209183624.png]]

##### Metasploit with module `exploit/multi/http/apache_mod_cgi_bash_env_exec`
Set RHOSTS an TARGETURI (where the .cgi file is, for example */gettime.cgi*), and exploit. It should establish a meterpreter session.

## Privilege Escalation
### Linux Kernel Exploitation
1. Identify kernel vulnerabilities.
2. Downloading, compiling and transferring kernel exploits onto the target system.

#### Tools
## Linux exploit suggester
When identifying kernel exploits, the kernel version and distribution release version are the most important pieces of information.

[Official repo](https://github.com/The-Z-Labs/linux-exploit-suggester)
1. Gain meterpreter session on the target.
2. Download the linux exploit suggester into the target (or upload it through the meterpreter session with the `upload` command).

Lets say the target is vulnerable to dirty cow. We can download the proposed exploit by LES.

If it is a C file, it may need to first install a compiler in the host, compile it and upload it to the target (similar to the the LES sh file).

## Cron Jobs
We will target on Cron jobs that will be run as the root user and will consequently provide us with root access.

In the video, there is a file */home/student/message* whose owner is root and permissions are limited to the owner. We assume that the root user is calling this file from somewhere. So we search the pattern “/home/student/message” (`-e /home/student/message`) recursively (`-r`), by line (`-n`) and matching the whole path inside the document (`-w`):

```sh
grep -rnw /usr -e "/home/student/message"
```