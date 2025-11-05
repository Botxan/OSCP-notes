Web vulnerability scanner to automate web server enumeration and scan web applications for vulnerabilities. Its a plugin of Metasploit.

Once in the msfconsole, load the extension:
```sh
load wmap
```

See all the commands:
```sh
wmap_<TAB>
```

Add a site:
```sh
wmap_sites -a <IP>
```

Set the target page:
```sh
wmap_targets -t <URL>
```

List set/obtained information:
```sh
wmap_sites -l
wmap_targets -l
```

List all the modules enabled for our target:
```
wmap_run -t
```

Run the modules:
```sh
wmap_run -e
```

In Metasploit
- We can use module `auxiliary/scanner/http/options` to check which HTTP methods are allowed by the website.
- Assume PUT is allowed. We can try to upload files with the module `auxiliary/scanner/http/http/put`. In the example, `/` path is not vulnerable to upload files via PUT method. However, file uploaded is possible in the `/data` path.