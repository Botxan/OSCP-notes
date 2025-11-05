Enumerate the SMB information:
```sh
crackmapexec smb <ip>
```

Enumerate users:
```sh
crackmapexec smb 10.129.204.177  -u <user> -p <password> --users
```
If anonymous user, `-u '' -p ''`.


