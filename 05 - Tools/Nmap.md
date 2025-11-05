## Flags

> [!summary]
> `-sn`: no port scan (ping sweep).
> `-p-`: all TCP ports.
> `-p 80,443`: specific ports.
> `-F`: only scan the top 100 most used ports.
> `-f`: fragment the packets. Add `--mtu <number>`: fragmentation with a specific size per fragment.
> `-sU`: UDP scan (outputs the UDP ports used in the target).
> `-v`. verbose. Print results immediately.
> `-sV`: service detection. Add `--version-intensity <number>`: more aggressive service detection. Number between 1 and 9, 9 the most aggressive.
 > `-O`: OS detection. Add `-osscan-guess`: more aggressive OS detection.
> `-sC`: script scan. Runs a set of nmap scripts on each of the ports to enumerate more information.
 > `-A`: combination of `-sV`, `-O` and `-sC` and `--traceroute`.
 > `T<0-5>`: timing templates. They are used to speed up/slow down the scan. By default, nmap decides what template to use based on the network infraestructure.
 > `--ttl <value>`: sets the IPv4 time-to-live, useful for firewall and NIDS evasion.
> `-o <filename>`: output to a file.
> --reason: provide more details regarding its reasoning and conclusions.
>
> Format types:
> `-o` default nmap format.
> `-oX` xml format

## Scan Types
### Host Discovery
#### Ping Sweep (`-sn`) (no port scan)
If the target is in the same LAN, nmap by default will use ARP. If we want to use. If we want to overwrite ARP resolution (and instead use ICMP traces), add `--send-ip`.

This option consists of:
- An ICMP echo request,
- a TCP SYN to port 443,
- a TCP ACK to port 80 and
- an ICMP timestamp request by default.
- If sent by an unprivileged user, only SYN packets are sent to ports 80 and 443 on the target.

Example:

```sh
sudo nmap -sn 192.168.2.0/24
```

#### TCP SYN (`-PS`)
By default, it sends a TCP SYN packet to the port 80 (but can be changed with `PS <ports>`. If the port is closed, the target responds with an RST packet. If open, the response is SYN ACK (so an RST packet is sent to reset the connection).

Is considered faster and stealthier, because it doesn’t go through the last SYN ACK in order to start the TCP connection.

The TCP SYN Ping Scan **is the most effective method to check if a host is up or not**.

Example:

```sh
sudo nmap -sn -PS 10.4.23.227
```

We set `-sn` to say to nmap to perform just host discovery. Then `-PS` will overwrite the configuration of `-sn` to send packets and will just send TCP SYN packet. If `-sn` is omitted, a port scanning is going to be performed.

Another example:

```sh
sudo nmap -sn -PS1-1000 10.4.23.227
```

#### TCP ACK (`-PA`)
An ACK is sent to the target. If an RST is received (since an ACK where a connection was not established), the host is up. If no response, either the host is down or it is behind a firewall.

Not very recommended tho, in these cases TCP SYN scan works better.

```sh
sudo nmap -sn -PA1-1000 10.4.23.227
```

#### ICMP (-PE)
- If we are scanning in a LAN, in order to make it work we must also add `--send-ip`.

```sh
sudo nmap -sn -PE 1-1000 10.4.23.227
```

Like ACK, not very recommended if SYN scan available.

#### UDP (-PU)

```sh
sudo nmap -sn -PU 1-1000 10.4.23.227
```

### Port scanning
#### TCP
##### SYN (`-sS`)
It is the default port scan, and generally the most efficient/stealthy. If the scan is performed as a non privileged user, the `-sS` is mandatory.

The main reason it is considered stealthier is because it doesn’t complete the three way handshake, and as a result, it avoids the creation of connection log entries in the target host.

##### Connect (`-sT`)
The default when SYN scan is not an option. This is the case when the **user does not have raw packet privileges** or is scanning IPv6 networks.
- It uses `connect` system call, and performs full three way handshake.
- After the handshake, the connection is closed with an \[RST, ACK\].

##### ACK (`-sA`)
- ACK bit set.
- If the target responds with RST, the port is either open or closed (unfiltered).
-** Useful when there is a firewall**. This scan will tell with ports are unfiltered.

##### Window (`-sW`)
- Almost the same as the ACK scan.
- Examines the TCP Window field of the RST packets returned.
- On specific systems, this can reveal that the port is open.

##### Null (`-sN`)
- All six flag bits to zero.
- Lack of reply → port is open or a firewall is blocking the packet
- Respond with RST → Port is closed

##### FIN (`-sF`)
- Same as null scan, but flag FIN set.
- Some firewalls will ‘silently’ drop the traffic without sending an RST.

##### Xmas (`-sX`)
- FIN, PSH and URG flags set.
- Response types are the same as FIN and Null scan.

##### Maimon (`-sM`)
- FIN and ACK flags set.
- Target should send RST packet as a response. However, certain BSD-derived systems drop the packet if it is an open port, exposing the open ports.
- Most targets systems respond with an RST packet regardless of whether the TCP port is open.
- This scan won’t work on most targets encountered in modern networks.

##### Custom (`--scanflags <flags>`)
- For example, to set SYN, RST and FIN simultaneously: `--scanflags RSTSYNFIN`

#### UDP Port Scan (`-sU`)
- Rest of usage is the same as a TCP scan.

### Service Detection (`-sV`)
You can control the intensity of with `--version-intensity LEVEL` where the level ranges between 0, the lightest, and 9, the most complete.
- `-sV --version-light` has an intensity of 2.
- `-sV --version-all` has an intensity of 9.

## Nmap Scripting Engine (NSE)
- List of scripts: `/usr/share/nmap/scripts/`.
- There are 4 categories of scripts:
	- **Auth**: authentication credentials (or bypassing them) on the target system.
	- **Broadcast**: discovery of the hosts not listed on the command line by broadcasting on the local network.
	- **Brute**: brute force attacks to guess authentication credentials.
	- **Default**: the ones executed with `-sC`or `-A` options. These scripts do not actively engage with the target system.
- If we want to know in which category is included a particular script (as well as some additional information):

```sh
nmap --script-help=mongodb-databases
```

### Common scripts
- `http-enum.nse`: basic enumeration from a website, including title, banner…

### Examples
#### Run default scripts

```sh
nmap -sC -p- 192.224.77.3
```

#### Run all scripts from a category

```sh
nmap --script=category ...
```

#### Run particular scripts

```sh
nmap --script=memcached-info,ftp-* -p- 192.224.77.3
```

In this case, all scripts matching `ftp*` pattern will be run.

## Evasion of Firewall and IDS
[Official documentation](https://nmap.org/book/man-bypass-firewalls-ids.html)
- The port scan with `-sA` will tell if there is a stateful firewall protecting the host.
![[Pasted image 20250131142427.png|cener]]

- `--ttl <value>`.
- `--data-length <number>`: appends random data to sent packets. Slows a bit the scan, but makes is less conspicuous.

### Spoofing (`-S SPOOFED_IP`)
- Need to guarantee that you can capture the response.
- In general, it is expected to specify the network interface (`-e`) and to explicitly disable ping scan `-Pn`. Therefore, instead of `nmap -S SPOOFED_IP <target>`, you will need to use `nmap -e NET_INTERFACE -Pn -S SPOOFED_IP <target>`.
- When you are on the same subnet as the target, you can also spoof the MAC address (`--spoof-mac SPOOFED_MAC`).
- Spoofing only works in a minimal number of cases. The attacker might resort to using decoys to make it more challenging to be pinpointed.
- Spoofing works better in combination with and idle scan (`-sI`).

#### Idle Scan (`-sI ZOMBIE_IP`)
- Requires an idle system connected tot he network that you can communicate with.
- Nmap will make each probe appear as if coming from the idle (zombie) host, then it will check for indicators whether the idle (zombie) host received any response to the spoofed probe.
- The idle (zombie) scan requires the following three steps to discover whether a port is open:
	1. Trigger the idle host to respond so that you can record the current IP ID on the idle host.
	2. Send a SYN packet to a TCP port on the target. The packet should be spoofed to appear as if it was coming from the idle host (zombie) IP address.
	3. Trigger the idle machine again to respond so that you can compare the new IP ID with the one received earlier.
- Possible scenarios:
	1. **The TCP port is closed:** target responds to the idle host with RST. Idle host does not respond; hence, its IP ID is not incremented.
	2. **The TCP port is opened:** target responds with the SYN/ACK to the idle host. The idle host responds to this unexpected packet with an RST packet, thus incrementing its IP ID.
	3. **Target machine does not respond at all due to firewall rules**: same result as with the closed port.

### Decoys (`-D DECOY1,ATTACKER_IP,DECOY2`)
- Example: `nmap -D 10.10.0.1,10.10.0.2,RND,RND,ME 10.10.241.201`
	- The third and fourth source IP addresses are assigned randomly.
	- The fifth source is going to be the attacker’s IP address.

### Fragmented packets (`-f`)
- The IP **data** will be divided into 8 bytes or less.
- Adding another `-f` (`-f -f` or `-ff`) will split the data into 16 byte-fragments instead of 8.
- You can change the default value by using the `--mtu`. However, you should always choose a multiple of 8.
![[Pasted image 20251013104522.png|center]]
- To aid in the reassembly on the recipient side, IP uses the identification (ID) and fragment offset.
- It is possible to increase the size of the packets using random bytes with `--data-length NUM`. It slows things down a little, but can make a scan slightly less conspicious by changing the default sizes of the packets of the scans.

## Timing and Performance
![[Pasted image 20250131173037.png]]

## Output Formats
`-oN`: normal format, as we see on the stdout normally.
`-oX`: in XML. Useful to import it later in MSF.
`-oS`: script kiddie format (its a joke). Is like a normal format but swapping ‘A’ by 4 and so on.
`-oG`: grepable format. Good for passing the output to another utility, for automation purposes.
`-oA`: combination of `-oN`, `-oX` and `-oG`.
`-v` or `-vv`: more verbose.

## Additional Examples

```sh
# Basic SYN (default) scan on the top 1000 ports:
nmap 10.4.19.218
```

> [!NOTE]
> This type of scan firstly performs a ping to check if the device is up. But Windows machines and some firewalls will block ping probes, so the scan may not work. In order to prevent nmap from performing the initial ping, we can add the `-Pn` flag.

```sh
###### Scan a range of hosts
nmap 10.0.1.227-234
nmap 10.4.23.227 10.4.23.228
nmap -iL targets.txt # an IP (or range as previous lines) per line
```

```sh
# Combined TCP and UPD host discovery in windows:
nmap -sn -PS21,22,25,80,445,3389,8080 -PU137,138 -T4 10.4.20.217
```

## Scan methodology
1. Scan the network or a host using `-sn`.
2. Once we know host/hosts is/are alive, perform port scanning (`-sS`), service detection (`-sV`), OS detection (`-O`) in all ports (`-p-`). Use template to speedup (`-T<number{0,5}>`)
3. Based on found services, execute nmap scripts.