## Login details

name: xxxxx
server name: xxxxx
username: xxxxx
password: xxxxx
IP Address: 192.168.xxx.xxx

## On first install of any Ubuntu Server, install updates:

```
sudo apt update && sudo apt dist-upgrade
```

## Install the QEMU guest agent for Linux distro. For ubuntu:

```
sudo apt install qemu-guest-agent
```

### Check the status of the QEMU service

```
systemctl status qemu-guest-agent.service
```

### Start if QEMU service is dead
```
sudo systemctl start qemu-guest-agent.service
```

This will fail if the QEMU Guest Agent option is disabled on the VM in proxmox options. Enable it in Proxmox, and reboot the VM. 

```
sudo poweroff
```
Once it powers off, restart the vm in proxmox, or boot cycle the server


## Install a web server -- Apache
```
sudo apt install apache2
```
