# Launching VMs using templates

## Full Tutorial
https://www.youtube.com/watch?v=t3Yv4OOYcLs&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=7 

Start by logging into the VM that you want to convert into a template. SSH is ideal.

## SSH Host Keys
We don't want to keep SSH host keys the same between VM's, so we need to remove them from the template.

Check the SSH host keys to see them:
```bash
ls -l /etc/ssh
```

## Search for cloud-init
Cloud init features a number of modules to master an image (.i.e. a template).

It allows you to autmoate tasks when setting up linux servers. For example, managing SSH host keys.

```
apt search cloud-init
```
If it is not installed run
```
sudo apt install cloud-init
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

Add any configuration files if needed. If this VM doesn't already have a QEMU agent installed, make sure to install it and enable it:

```bash
sudo apt install qemu-guest-agent
sudo systemctl start qemu-guest-agent.service
sudo systemctl status qemu-guest-agent.service
```

## Convert to Template

Power off the vm:
```bash
sudo poweroff
```

In ProxMox, right click on the VM, and select Convert To Template. The icon for the VM should be a template icon now

## Hardware Cleanup

### Remove .iso drive
A good practice is to remove the attachment to the .iso for the virtual disk used to originally create the vm

In ProxMox, on the template vm, go to Hardware and select the .iso drive (CD/DVD Drive)

Click Edit > Do not use any media > OK

### Add a cloud init drive
In ProxMox, on the template vm, go to Hardware:

Click Add > Cloudinit Drive > Storage = local-lvm > Add

Select Cloud-Init Menu > User = (default username), Password = (default password), etc > Regenerate Image


## Create a new VM from Template:

In ProxMox, Right click the template > clone > mode= Full Clone, target storage = local-lvm, name=<your-server-name> > Clone

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