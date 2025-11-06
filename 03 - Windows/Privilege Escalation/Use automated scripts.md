## WinPEAS

> WinPEAS can be downloaded [here](https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS)

WinPEAS is a script developed to enumerate the target system to uncover privilege escalation paths. The output from winPEAS can be lengthy and sometimes difficult to read, so it would be a good practice to always redirect the output to a file, as shown below:

```cmd
winpeas.exe > outputfile.txt
```

## PrivescCheck

> PrivescCheck can be downloaded [here](https://github.com/itm4n/PrivescCheck)

PrivescCheck is a PowerShell script that searches common privilege escalation on the target system. It provides an alternative to WinPEAS without requiring the execution of a binary file.

> [!attention]
> To run PrivescCheck on the target system, we may need to bypass the execution policy restrictions. To achieve this, we can use the `Set-ExecutionPolicy` cmdlet as shown below.
>```powershell
>Set-ExecutionPolicy Bypass -Scope process -Force
>. .\PrivescCheck.ps1
>Invoke-PrivescCheck
>```

## WES-NG: Windows Exploit Suggester - Next Generation

> WES-NG can be download [here](https://github.com/bitsadmin/wesng)

Some exploit suggesting scripts (e.g. winPEAS) will require you to upload them to the target system and run them there. This may cause antivirus software to detect and delete them. To avoid making unnecessary noise that can attract attention, you may prefer to use WES-NG, which will run on the attacking machine.

Before using it, update the database:

```sh
wes.py --update
```

To use the script, we will need to run the `systeminfo` command on the target system and redirect the output to a .txt file. Once this is done, wes.py can be run as follows:

```sh
wes.py systeminfo.txt
```

## Metasploit
If we already have a Meterpreter shell on the target system, we can use the `multi/recon/local_exploit_suggester`.