Web Application Firewall Fingerprinting Tool. Identify if a website is using a WAF.

[Repository](https://github.com/enablesecurity/wafw00f/wiki/) and [documentation](https://github.com/EnableSecurity/wafw00f/wiki/Usage#testing-for-all-possible-waf-instances)
## Usage
```sh
wafw00f hackersploit.org
```

Test all possible WA instances (instead of stopping on the first match):
```sh
wafw00f http://example.com -a
```