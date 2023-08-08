# Installing Linux Containers (LXC) on ProxMox

## NOTE: To install Docker inside the LXC, see https://www.wundertech.net/how-to-set-up-docker-containers-in-proxmox/ 


## Tutorial

The tutorial can be found: https://www.youtube.com/watch?v=aGfPjdUM2cM&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=8 

Follow the tutorial in the link above, then continue here for further configuration and troubleshooting:

## Allowing SSH Traffic

### Enable SSH service on startup
If the selected template does not allow SSH traffic to be started by default, you may need to fix this. The ssh server is socket-activated in those distros/templates - see the corresponding .socket unit file. you can either adapt that (systemctl edit ssh.socket and add your port) or use ssh.service instead (systemctl disable ssh.socket; systemctl enable ssh.service)

It is recommended to just enable the ssh.service on startup by default rather than edit the ssh.socket file config. To do this, run the command:

```bash
systemctl disable ssh.socket; systemctl enable ssh.service
```
### Allow SSH traffic through the Container Firewall

In addition to , you may need to allow SSH traffic through the firewall. In ProxMox, on the Container's Firewall tab, add a new rule and set these settings:

- **Direction**: Choose in (this means incoming traffic to the container).
- **Enable**: Check this box to enable the rule.
- **Action**: Choose ACCEPT (this means you're allowing the traffic).
- **Interface**: Leave this empty unless you have multiple network interfaces and you want to specify one. In most cases, it's fine to leave it blank.
- **Macro**: Choose SSH from the dropdown. This is a predefined setting for allowing SSH traffic.
- **Protocol**: This will auto-fill once you choose the SSH macro, but if it doesn't, choose TCP.
- **Source**: Leave this empty to allow SSH from any IP address. If you want to restrict SSH access to a specific IP or range, you'd enter it here.
- **Source port**: Leave this empty.
- **Destination**: Leave this empty.
- **Dest. port**: This will auto-fill once you choose the SSH macro, but if it doesn't, enter 22.
- **Comment**: You can enter something like "Allow SSH" for your reference.
- **Log Level**: Choose nolog unless you want to log every SSH connection attempt.

### NOTE: if connecting via PuTTY, saved sessions can become corrupt. Just enter the connection details manually instead. You can try deleting saved configs and recreating them.

## Adding Users to Container:

By default, ssh login to the root user is not allowed (see /etc/ssh/sshd_config). If you want to permit ssh login into root, you can uncomment change the #PermitRootLogin and change the setting from 'prohibit password' to 'yes'. However, this is not generally recommended. Instead, you should create a user account and add the user to the sudo group. On the container, run:

```bash
adduser <username>
```

Go through all of the prompts to create the new user.

## Adding user to Sudo group
In order to run sudo / admin commands on the new user, add them to the sudo group with:

```bash
usermod -aG sudo <username>
```

## Converting to template
To convert the container to a template see [`ct-container-templates.md`](ct-container-templates.md)