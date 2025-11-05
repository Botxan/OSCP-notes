Connect:
```sh
ftp <host>
```

## Nmap Scripts
| Module  | Description                              |
| ------- | ---------------------------------------- |
| ft-anon | Check if server supports anonymous login |

## Metasploit Modules
| Module                            | Description                    |
| --------------------------------- | ------------------------------ |
| auxiliary/scanner/ftp/ftp_version | FTP Version Scanner            |
| auxiliary/scanner/ftp/ftp_login   | FTP Authentication Scanner     |
| auxiliary/scanner/ftp/anonymous   | Anonymous FTP Access Detection |

## Brute-Force with Hydra
```sh
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt <host> -t 4 ftp://<host>
```

>[!NOTE]
>We set the threads (`-t` to `4` because more threads my provoke the service to stop or crash).
## Manual Exploitation
Check anonymous login:
```sh
ftp <host>
anonymous
anonymous
```

Check for vulnerabilities (in this case ProFTPD 1.3.5a):
```sh
searchsploit ProFTPD
```

### Wordlists
#### Users
/usr/share/metasploit-framework/data/wordlists/common_users.txt
#### Passwords
/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt