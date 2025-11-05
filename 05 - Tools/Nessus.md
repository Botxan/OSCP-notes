Proprietary vulnerability scanner. It can scan hosts, scan ports, grab banners, get service and versions of open ports. Nessus has a DB that maps services and versions to vulnerabilities.

We can export the results of Nessus scan to MSF for analysis and exploitation by clicking on the web interface in Export > Nessus. Then, in msfconsole:
```sh
db_import nessus_scan.nessus
```

There is a free version available, but only allows to scan 16 IPs. It doesn’t come preinstalled in Kali. We need to register in the website and download it.

Then it will open a web server on port 8834.