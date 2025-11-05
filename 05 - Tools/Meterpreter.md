Display current user:
```sh
meterpreter > getuid
```

Get general information of the system:
```sh
meterpreter > sysinfo
```


Once inside, we can executes ruby scripts or Metasploit Post modules in the context of the
meterpreter session. Example:
```sh
meterpreter > run post/multi/recon/local_exploit_suggester
```

>[!Notes]
> Some exploits need to be executed multiple times. For example, the `windows/local/bypassuac_eventvwr` module.
> 


- *Living in* a process. Often when we take over a running program we ultimately load another shared library into the program (a dll) which includes our malicious code. From this, we can spawn a new thread that hosts our shell.
- A process that has similar privileges as **lsass.exe** (and also restarts if crashes when attempting to migrate to it) is the printer service (spoolsv.exe).

To migrate to a process (by name):
```sh
migrate -N <process name>
```

If the process is privileged and we run `getuid`, we will see `NT AUTHORITY\SYSTEM`.

# Modules
Load modules:
```
load <module name>
```

- **Kiwi**: to perform various types of credential-oriented operations, such as dumping passwords and hashes, dumping passwords in memory, generating golden tickets, and much more.
