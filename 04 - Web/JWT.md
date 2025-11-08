Tools to manipulate JWT tokens:
- [JWT.io](https://www.jwt.io/)
- [JWT decoder](https://fusionauth.io/dev-tools/jwt-decoder)
- [Cyberchef](https://gchq.github.io/CyberChef/)

# Development mistakes
1. **Signature is not being verified**: token can be freely modified.
2. **Downgrade to None**: if the token decoded this way:
```python
header = jwt.get_unverified_header(token)

signature_algorithm = header['alg']

payload = jwt.decode(token, self.secret, algorithms=signature_algorithm)
```

We can change the algorithm to None.

3. **Weak Symmetric Secrets**: if weak secret is used, it may be possible to perform offline cracking to recover the secret:
	1. Save the JWT to a text file (e.g. *jwt.txt*)
	2. Download or create a common JWT secret list (e.g. [jwt.secrets.list](https://raw.githubusercontent.com/wallarm/jwt-secrets/master/jwt.secrets.list))
	3. Use Hashcat to crack the secret using `hashcat -m 16500 -a 0 jwt.txt jwt.secrets.list`
4. **Signature Algorithm Confusion**: Similar to the `None` downgrade attack. If an asymmetric signing algorithm, for example, RS256 is used, it may be possible to downgrade the algorithm to HS256. Some libraries would default back to using the public key as the secret for the symmetric signing algorithm.
5. **Token lifetime**: if the value of the `exp` claim is too large, the token would be valid for too long or might even never expire.
6. **Cross-Service Relay Attacks**: In cases where a single authentication system serves multiple applications, the `audience` claim can indicate which application the JWT is intended for. However, the enforcement of this `audience` claim has to occur on the application itself. 
   In the following example, the app B provides a token with the admin role set for the app B:
   ![[Captura de pantalla 2025-11-07 175017.png|center]]
   Now, app A should check that the `audience` claim in order to invalidate the token. However, this is not the case:
   ![[Pasted image 20251107175832.png|center]]