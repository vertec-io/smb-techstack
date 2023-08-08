# Recommended Proxmox Conventions

The followign conventions are recommended to maintain an organized virtual machine and containter workflow

## ID Conventions

- **1000 - 1999** ----> VM Templates
- **2000 - 2999** ----> Container Templates
- **3000 - 3999** ----> Linux VMs
- **4000 - 4999** ----> Linux Containers
- **5000 - 5999** ----> Windows VMs


## Name Conventions

\<os><server?>-<vm/ct>-<cli/gui>-\<index-from-ID>-<desc?>

*? indicates optional/as applicable \
** desc max characters = 10

#### Example:

- Linux Server VM as CLI with VM ID 3023 ------------------------------------> **linuxserver-vm-cli-23**
- Linux Server VM as GUI with VM ID 3051 -----------------------------------> **linuxserver-vm-gui-51**
- Linux Server Container as CLI with VM ID 4046 ---------------------------> **linuxserver-ct-cli-46**
- Linux Server Container as GUI with VM ID 4038 --------------------------> **linuxserver-ct-gui-38**
- Linux Server Container as GUI with VM ID 4075 called "MyApp" ------> **linuxserver-ct-gui-38-MyApp**
- Windows Server VM as GUI with VM ID 5012 -------------------------------> **winserver19-vm-gui-12**
- Windows Desktop VM as GUI with VM ID 5035 for user U12345 ------> **windows10-vm-gui-35-U12345**


## Useful Commands

### Managing CT/VM state

Generally VM states can be modified with the ```qm``` command. CT states can be modified with the ```pct``` command.


```
<qm/pct> <command> <VMID>

```
#### Example
```bash
qm shutdown 3012
pct shutdown 4056
```

### Valid Commands:
```
shutdown
reboot
destroy
unlock
```

use ```qm help``` or ```pct help``` for more command details