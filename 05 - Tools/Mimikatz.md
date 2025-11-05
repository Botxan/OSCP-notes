
| Command                    | Source                                                          | Extracts                                | Privileges Required | Use Case                                      |
| -------------------------- | --------------------------------------------------------------- | --------------------------------------- | ------------------- | --------------------------------------------- |
| `sekurlsa::logonpasswords` | LSASS process (RAM)                                             | Logged-in user passwords/hashes         | Admin/SYSTEM        | Real-time credential dumping                  |
| `lsadump::sam`             | SAM database (`C:\Windows\System32\Config\SAM`)                 | Local account NTLM hashes               | SYSTEM              | Extracting local user hashes for cracking/PTH |
| `lsadump::secrets`         | Windows Registry (`HKLM\SYSTEM\CurrentControlSet\Services\LSA`) | Stored passwords (services, auto-login) | SYSTEM              | Recovering stored service/domain credentials  |

>[!NOTE]
>Mimikatz does not make a copy of the SAM database 