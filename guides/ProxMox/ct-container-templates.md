# ProxMox - Configurating LXC Container Templates

## Full Tutorial: https://www.youtube.com/watch?v=J29onrRqE_I&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=9 

## Create a new container
If a container doesn't exist to be converted to a template, first create a container. See [`ct-linux-container.md`](ct-linux-container.md)

## Update the LXC

First make sure everything is up to date. Run:

```bash
sudo apt update && sudo apt dist-upgrade
```
## Remove the SSH host keys
Before converting the container to a template, make sure to remove the SSH host keys on this container to prevent connection issues due to conflicting ssh keys once containers are cloned from this template:

```bash
cd /etc/ssh
ls #shows the host keys. You should see some that have the ssh_host_ prefix
sudo rm ssh_host_* #this deletes the host keys
ls #verify the host keys have been deleted
```

## Configure the machine ID
The machine-id will also need to be cleared out in the template so a new one is generated for each new container cloned from this template.

Go into the machine id dir and delete the contents of the machine ID file
**NOTE:** Simply deleting this file does not work.

```bash
cat /etc/machine-id
sudo truncate -s 0 /etc/machine-id
```
Now check for symbolic link to the machine ID file to make sure this is still working.

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
This template is now comlete, and new containers can be created by cloning it.

# Configuring Template Clones
Once the template has been cloned into a new virtualized container, a couple of configurations need to be completed.

## Regenerate host SSH keys
LXC containers don't support cloud-init, so on the creation of a new container from a template, the SSH host keys will need to be regenerated manually:

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

## Users

## Adding Users to Container:

To add new users to this container, run:

```bash
adduser <username>
```

Go through all of the prompts to create the new user.

## Adding user to Sudo group
In order to run sudo / admin commands on the new user, add them to the sudo group with:

```bash
usermod -aG sudo <username>
```