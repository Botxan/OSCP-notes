---
date: 01/02/2025 13:16
tags: 
cssclasses:
---
Types of modules
- **Auxiliary**: 
	- Scanning, discovery and fuzzing. 
	- Can be used during information gathering phase, as well as in the post exploitation phase.

# Commands
Set global variables:
```sh
setg <name> <value>
setg RHOSTS 192.91.46.3
```

Get information about module
```sh
use <module>
info
```

Check gathered information:
```sh
hosts
services
loot # like database dumps
creds
vulns
```

Analyze the metasploit database and provide exploits for the found services:
```sh
analyze
```

# Before starting
First run `postgresql` database:
```sh
service postgresql start
```

Run `msfconsole`:
```sh
msfconsole
```

Once in the msfconsole, show if database is connected:
```sh
db_status
```

Create a workspace:
```sh
workspace -a Win2k12
```
# Search
By (containing) string:
```sh
search <string>
```

By type
```sh
search type:auxiliary <string>
```

By cve year and name:
```sh
search cve:2019 name:rdp
```
# Nmap
Import Nmap XML results:
```sh
db_import path/to/nmap_output.xml
```

Perform a Nmap scan directly from Msf console:
```sh
db_nmap <nmap flags and arguments>
```

> Scan results are automatically imported to the database.

# Port Scanning with Auxiliary Modules
Modules
- `scanner/portscan/tcp`

# Plugins
## [db_autopwn](https://github.com/hahwul/metasploit-autopwn)
Looks at your metasploit database and checks the ports open and services running on the scanned target systems, and provides a list of exploit modules that can be used to each of the services.

Load it into metasploit and show usage:
```sh
load db_autopwn
db_autopwn
```

Search for exploits for hosts that have the SMB port opened:
```sh
db_autopwn -p -t -PI 445
```