-  Check if the url contains parameters that can lead to new requests.

## Changing subdomain in a request
![[Pasted image 20250918152257.png]]

> Expected request: *http://website.thm/stock?server=api&id=123*

1. The server will take these parameters and do a request like this: *http://\<server\>.website.thm/api/stock/item?id=\<id\>*

The goal is to get user information from this endpoint: */api/user*

How?

Request: http://website.thm/stock?server=api.website.thm/api/user&x=&id=123

The server then will do a request like this: *http://api.website.thm/api/user?x=.website.thm/api/stock/item?id=123*

Everything from *x=* onwards will be ignored (since the endpoint */api/users* does not use *x* parameter). Thus, the request becomes *http://api.website.thm/api/user*.
