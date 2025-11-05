# Metasploit Modules
| Module                                 | Description                  |
| -------------------------------------- | ---------------------------- |
| auxiliary/scanner/ssh/ssh_version      | SSH Version Scanner          |
| auxiliary/scanner/ssh/ssh_enumusers    | SSH Username Enumeration     |
| auxiliary/scanner/ssh/ssh_login        | SSH Login Check Scanner      |
| auxiliary/scanner/ssh/ssh_login_pubkey | SSH Public Key Login Scanner |

# Brute-Force with Hydra
```sh
hydra -L <user-wordlist> -P <password-wordlist> <host> -t 4 ssh
```