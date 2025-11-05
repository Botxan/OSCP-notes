Enumerate subdomains of websites using OSINT.
[Official repository](https://github.com/aboul3la/Sublist3r)

Installation:
```sh
sudo apt install sublist3r
```

Search by domain using all engines:
```sh
sublist3r -d hackersploit.org
```

Search by domain with specific engines:
```sh
sublist3r -d hackersploit.org -e google,yahoo
```

>[!NOTE]
>Google has a rate limit for queries, and sublist3r makes so much request that Google may block it, so sometimes it may not get results. In order to bypass this restriction, we can use VPN (or simply use other search engines)

It also provides mechanisms to make a subdomain enumeration with bruteforce.