[Windows O.S.](https://www.microsoft.com/en-us/windows) is a prime target for attackers given the threat surface and its popularity.

Most of the Windows vulnerabilities **exploits** are publicly available, making them simple to use:
- Threat surface is fragmented, depending on the Win O.S. version.
- The older the O.S. version, the more vulnerable to attacks.
- All of Windows operating systems share a similarity according to the development model:
	- `C` programming language - leads to buffer overflows, arbitrary code execution, etc.
	- No default security practices applied - must be sistematically handled by the company.
	- Patching by Microsoft is not immediate, or versions are out of support/patching.
- To name a few, Windows `XP`, `7`, `Server 2008` and Server 2012, are still used by many companies and are _**largely vulnerable**_, leaving the systems open to new attack vectors.
	- Cross platform vulnerabilities, `e.g.` SQL injections, cross-site scripting (on IIS web servers).
- Physical attacks, `e.g.` malicious USB drives, theft, etc.

## Frequently Exploited Services

| Protocol/Service                                                    | Ports                  | Purpose                                                                                                                                                     |
| ------------------------------------------------------------------- | ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [[⚙ IIS - WebDAV\|Microsoft IIS (Internet Information Services)]]   | TCP ports `80`/`443`   | Microsoft Web server for Windows, hosting web applications                                                                                                  |
| [[⚙ IIS - WebDAV\|WebDAV (Web Distributed Authoring & Versioning)]] | TCP ports `80`/`443`   | HTTP extension that allows clients to update, delete, move and copy files on a web server. Used to enable a web server to act as a file server.             |
| [[⚙ SMB - PsExec\|SMB/CIFS (Server Message Block Protocol)]]        | TCP port `445`         | Network file sharing protocol that is used to facilitate the sharing of files and peripherals between computers on a local network (LAN).                   |
| [[⚙ RDP\|RDP]]                                                      | TCP pot `3389`         | Proprietary GUI remote access protocol developed by Microsoft and is used to remotely authenticate and interact with a Windows system. Disabled by default. |
| [[⚙ WinRM\|WinRM (Windows Remote Management Protocol)]]             | TCP ports `5986`/`443` | Windows remote management protocol that can be used to facilitate remote access with Windows systems. Disabled by default.                                  |
