## Login details

name: xxxxx
server name: xxxxx
username: xxxxx
password: xxxxx
IP Address: 192.168.xxx.xxx

## On first install of any Ubuntu Server, install updates:
These commands are best done by SSH into the vm
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


## Installing a GUI for Ubuntu Server
It is generally not recommended to install a GUI for production uses, as the GUI takes up additional resources on the system and adds additional complexity and overhead to maintaining updates and security patches. However, for experimenting and testing, it can be useful

This guide provides a more comprehensive instructions set:
https://www.makeuseof.com/install-desktop-environment-gui-ubuntu-server/ 

The commands require first to install the desktop, as well as a desktop manager. It is preferred to install a light desktop manager (lightdm)
```
sudo apt install ubuntu desktop

sudo apt install lightdm

sudo systemctl start lightdm.service
```

reboot the vm for changes to take effect

## Uninstalling the GUI
To remove the GUI and lightdm:
```
sudo apt autoremove ubuntu-desktop
sudo systemctl stop lightdm.service
sudo apt autoremove lightdm
```
reboot the vm for changes to take effect


## Remoting into the Ubuntu Desktop

Yes, you can remote into an Ubuntu Desktop from a Windows computer to access the graphical desktop environment. There are several ways to do this, but one common method is to use a VNC (Virtual Network Computing) server on the Ubuntu machine and a VNC client on the Windows machine. Here's a step-by-step guide:

### On the Ubuntu Desktop:

#### 1. **Install a VNC Server**
   - Open a terminal and run the following commands to install a VNC server like `x11vnc`:
     ```bash
     sudo apt-get update
     sudo apt-get install x11vnc
     ```

#### 2. **Set a Password for VNC Connections**
   - Run the following command to set a password:
     ```bash
     x11vnc -storepasswd
     ```

#### 3. **Start the VNC Server**
   - Run the following command to start the VNC server:
     ```bash
     x11vnc -usepw
     ```

    - Optionally, to enable ncache for VNC client-side pixel caching for faster performance and a smoother desktop experience, use:
    ```bash
    x11vnc -ncache 10 -ncache_cr -usepw
    ```

#### 4. **Configure the VNC Server to Start Automatically (Optional)**
   - If you want the VNC server to start automatically when the Ubuntu Desktop starts, you can create a systemd service or add the command to your startup applications.

   No worries at all! These are great questions, and I'm here to help.

### Do the Settings Persist?

The settings for `-ncache` and `-ncache_cr` do not persist between x11vnc sessions. You would have to pass these arguments each time you start the x11vnc server.

### Correct Command with Optimizations

The correct command to start the x11vnc server with the optimizations would be:

```bash
x11vnc -ncache 10 -ncache_cr -usepw
```

The `-usepw` option is to prompt for the password you've set earlier.

### Starting x11vnc Automatically on Startup

You can set up x11vnc to start automatically when the Ubuntu Desktop starts. Here's a general guide to doing that:

1. **Create a Script to Start x11vnc**
   - Create a new script file (e.g., `start-x11vnc.sh`) with the following content:
     ```bash
     #!/bin/bash
     x11vnc -ncache_cr -usepw -rfbport 5901 -forever -shared -geometry 1900x1055
     ```
     -ncache_cr enables smoother window connections
     -usepw requires password to remote in
     -frbport 5901 specifies the port to listen on
     -forever allows clients to connect and disconnect from sessions multiple times (default is x11vnc closes after first client disconnect)
     -shared allows multiple clients to connect at a time
     -geometry specific the window size (set to a default screen size slightly smaller than expected client size to prevent overflow on client side -- i.e. scrollbars)


2. **Make the Script Executable**
   - Run the following command to make the script executable:
     ```bash
     chmod +x /path/to/start-x11vnc.sh
     ```

3. **Create a Systemd Service**
   - Create a new file (e.g., `/etc/systemd/system/x11vnc.service`). You can do this by running:

    ```bash
    sudo nano /etc/systemd/system/x11vnc.service
    ```
Then, paste the following content into the file, making sure to update the ExecStart path to match where you saved your script:

     ```ini
     [Unit]
     Description=Start x11vnc at startup.
     After=multi-user.target

     [Service]
     Type=simple
     ExecStart=/path/to/start-x11vnc.sh

     [Install]
     WantedBy=multi-user.target
     ```

1. **Enable the Service**
   - Run the following commands to enable and start the service:
     ```bash
     sudo systemctl enable x11vnc.service #Start at boot
     sudo systemctl start x11vnc.service
     ```
Now, x11vnc should start automatically when the Ubuntu Desktop starts, and you won't have to manually log in and start it each time.

Some other usefull settings
```bash
sudo systemctl daemon-reload
sudo systemctl restart x11vnc.service
sudo systemctl status x11vnc.service
sudo systemctl stop x11vnc.service
sudo system disable x11vnc.service #disable starting at boot
```

#### 5. **Check Firewall Settings**
If you have a firewall enabled on the Ubuntu Desktop, make sure it's configured to allow VNC connections (default port 5900).

On Ubuntu, you can manage the firewall using the Uncomplicated Firewall (UFW) tool. Here's how you can check and configure the firewall settings to allow VNC connections:

##### 1. **Check if UFW is Installed**
   - If UFW is not installed, you can install it with:
     ```bash
     sudo apt-get install ufw
     ```

##### 2. **Enable UFW**
   - If UFW is not already enabled, you can enable it with:
     ```bash
     sudo ufw enable
     ```

##### 3. **Allow VNC Connections**
   - By default, VNC operates on port 5900, but it may conflict with other services running. An option is to specify port 5901 or another. You can allow connections on this port with:
     ```bash
     sudo ufw allow 5901
     ```

##### 4. **Check the Firewall Status**
   - You can check the status of UFW, including the rules that are currently in place, with:
     ```bash
     sudo ufw status
     ```

This will show you a list of the rules and whether UFW is active. You should see a line like this, indicating that VNC connections are allowed:

        ```
        5900                       ALLOW       Anywhere
        ```

##### Note

- If you've configured x11vnc to use a different port, you'll need to adjust the `ufw allow` command accordingly.
- If you're not using UFW and instead have a different firewall configuration, the steps to allow VNC connections will be different.

### On the Windows Computer:

#### 1. **Install a VNC Client**
   - Download and install a VNC client like [TightVNC](https://www.tightvnc.com/download.php), [RealVNC](https://www.realvnc.com/en/connect/download/viewer/), or [TigerVNC](https://tigervnc.org/).

#### 2. **Connect to the Ubuntu Desktop**
   - Open the VNC client and enter the IP address of the Ubuntu Desktop, followed by `:0` (e.g., `192.168.86.102:5901`).
   - Enter the password you set earlier.

You should now be able to see and interact with the Ubuntu Desktop from your Windows computer.
