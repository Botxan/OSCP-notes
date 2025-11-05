Port 25.
# Metasploit Modules
| Module                              | Description                   |
| ----------------------------------- | ----------------------------- |
| auxiliary/scanner/smtp/smtp_version | SMTP Banner Grabber           |
| auxiliary/scanner/smtp/smtp_enum    | SMTP User Enumeration Utility |
# Labs
## Postfix Recon: Basics
### 1. What is the SMTP server name and banner.
Just run the scanner/smtp/smtp_version module.
- Server name: Postfix.
- Banner:  “openmailbox.xyz ESMTP Postfix: Welcome to our mail server.\x0d\x0a”.

### 2. Connect to SMTP service using netcat and retrieve the hostname of the server (domain name).
```sh
nc demo.ine.local 25
HELO demo.ine.local
```
Hostname is openmailbox.xyz.

### 3. Does user “admin” exist on the server machine? Connect to SMTP service using netcat and check manually.
```sh
VRFY admin@openmailbox.xyz
```
Yes.
### 4. Does user “commander” exist on the server machine? Connect to SMTP service using netcat and check manually.
```sh
VRFY commander@openmailbox.xyz
```
No.

### 5. What commands can be used to check the supported commands/capabilities? Connect to SMTP service using telnet and check.
```
EHLO demo.ine.local
```

### 6. How many of the common usernames present in the dictionary /usr/share/commix/src/txt/usernames.txt exist on the server. Use smtp-user-enum tool for this task.
```sh
smtp-user-enum -U /usr/share/commix/src/txt/usernames.txt -t demo.ine.local
```
8

### 7. How many common usernames present in the dictionary /usr/share/metasploit-framework/data/wordlists/unix_users.txt exist on the server. Use suitable metasploit module for this task.
20

### 8. Connect to SMTP service using telnet and send a fake mail to root user.
```sh
telnet demo.ine.local 25
HELO attacker.xyz
mail from: admin@attacker.xyz
rcpt to: root@openmailbox.xyz
data
Subject: Hi root
Hello,
This is a fake mail sent using telnet command.
From,
Admin
.
```

### 9. Send a fake mail to root user using sendemail command.
```sh
sendemail -f admin@attacker.xyz -t root@openmailbox.xyz -s demo.ine.local -u Fakemail -m "Hi root, a fake from admin" -o tls=no
```