[Google Hacking Database](https://www.exploit-db.com/google-hacking-database): a lot of dorks to find specific information.

# Examples
Show only pages of ine.com (or subdomains):
```google
site:ine.com
```

Same but that contain “admin” in the url:
```google
site:ine.com inurl:admin
```

Enumerate subdomains:
```google
site:*.ine.com
```

Same but with admin in the title:
```google
site:*.ine.com intitle:admin
```

Find pfs:
```google
filetype:pdf
```

Find information of employees:
```google
site:ine.com employees
```

Find directory listings:
```google
intitle:index of
```

Find previous version of a webpage:
```google
cache:ine.com
```