- **Administrators**: they can change any system configuration parameter and access any file in the system.
- **Standard Users**: typically these users can not make permanent changes or essential changes to the system and are limited to their files.

# Default local accounts
- **SYSTEM/LocalSystem**: An account used by the operating system to perform internal tasks. It has full access to all files and resources available on the host with even higher privileges than administrators.
- **Local Service**: Default account used to run Windows services with "minimum" privileges. It will use anonymous connections over the network.
- **Network Service**: Default account used to run Windows services with "minimum" privileges. It will use the computer credentials to authenticate through the network.