# Unattended Windows Installations
Administrators may leave credentials when using Windows Deployment Services in the following locations:
- C:\Unattend.xml
- C:\Windows\Panther\Unattend.xml
- C:\Windows\Panther\Unattend\Unattend.xml
- C:\Windows\system32\sysprep.inf
- C:\Windows\system32\sysprep\sysprep.xml

Check for things like:
```xml
<Credentials>
    <Username>Administrator</Username>
    <Domain>thm.local</Domain>
    <Password>MyPassword123</Password>
</Credentials>
```

# Powershell History
```cmd
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

```powershell
$Env:userprofile\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

# Saved Windows credentials
`cmdkey` creates, lists, and deletes stored user names and passwords or credentials.
```cmd
cmdkey /list
```

It will not show passwords, but we can use the listed credentials with the `runas` command and the `/savecred` option:
```cmd
runas /savecred /user:admin cmd.exe
```

# IIS Configuration
The configuration of websites on IIS is stored in a file called `web.config` and can store passwords for databases or configured authentication mechanisms. We can find web.config in one of the following locations:
- C:\inetpub\wwwroot\web.config
- C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
Quick way to find database connection strings on the file:
```cmd
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
```

# Retrieve Credentials from Software: PuTTY
To retrieve stored proxy credentials:
```cmd
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```