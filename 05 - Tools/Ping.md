Simple ping request:

```sh
ping 8.8.8.8
```

## Linux
### Single request

```sh
ping -c 10.4.31.111
```

### Single request to each of the IPs in the network

```sh
ping -b -c 1 10.10.23.0
```

## Windows
### Single ICMP

```powershell
ping -n 10.4.31.111
```