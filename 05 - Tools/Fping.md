More efficient that `ping` for host discovery in a network

## Flags
- `-a`: show targets that are alive.
- `-g`: generate target list (only if `-f` specified) (give start and end IP in the target list, or a CIDR address) (ex. `fping -g 192.168.1.0 192.168.1.255` or `fping -g 192.168.1.0/24`)).
- `-S` set source address.

### Discover hosts in the network

```sh
fping -a -g 10.10.23.0/24 2>/dev/null
```