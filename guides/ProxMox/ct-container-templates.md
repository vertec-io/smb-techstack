# ProxMox - Configurating LXC Container Templates

## Full Tutorial: https://www.youtube.com/watch?v=J29onrRqE_I&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=9 

## Update the LXC

First make sure everything is up to date. Run:

```bash
sudo apt update && sudo apt dist-upgrade
```
## Configure SSH

go into the etc/ssh dir and show files and remove ssh_host keys

```
cd /etc/ssh
ls
sudo rm ssh_host_*
```

## Configure the machine ID

go into the machine id dir and delete the contents of the machine ID file
**NOTE:** Simply deleting this file does not work.

```bash
cat /etc/machine-id
sudo truncate -s 0 /etc/machine-id
```

Now check for symbolic link to the machine ID file

```bash
ls -l /var/lib/dbus/machine-id
```

This should output
```
 /var/lib/dbus/machine-id -> /etc/machine-id
```

The -> symbol indicates this is a symbolic link. If this is not a symbolic link, then create one and verify the link exists and machine-id is now empty:

```bash
sudo ln -s /etc/machine-id /var/lib/dbus/machine-id
ls -l /var/lib/dbus/machine-id
cat /etc/machine-id
```

## Clean the template
Clear all existing installed packages
```bash
sudo apt clean
```

Remove any unused/orphaned packages
```bash
sudo apt autoremove
```

#After Cloning the Template to a New LXC
## Regenerate host SSH keys

cloud-init should generate ssh keys for the host automatically, but if it doesn't SSH remote connections will fail. In this case, you can generate them manually with 

```bash
sudo dpkg-reconfigure openssh-server
```

## Update the hostname
Update the hostname to match the name of the VM in ProxMox

```bash
sudo nano /etc/hostname #edit the file contents to match the correct hostname
sudo nano /etc/hosts #edit the file content line for the ip address assigned to the template hostname to match the VM name
sudo reboot
```