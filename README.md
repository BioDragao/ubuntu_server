# ubuntu_server


# Mounting VirtualBox shared folders on Ubuntu Server 18.04 LTS (Bionic Beaver)
This guide will walk you through the steps on how to setup a VirtualBox shared folder inside your Ubuntu Server guest. 

## Prerequisites
This guide assumes that you are using the following setup:
- [Oracle VM VirtualBox version 6.0.10](https://download.virtualbox.org/virtualbox/6.0.10/VirtualBox-6.0.10-132072-Win.exe) with [Extension Pack](https://download.virtualbox.org/virtualbox/6.0.10/Oracle_VM_VirtualBox_Extension_Pack-6.0.10.vbox-extpack) installed
- Microsoft Windows 7 SP1 as the host OS
- A fresh install of [Ubuntu Server 18.04.2 LTS](https://ubuntu.com/download/server/thank-you?version=18.04.2&architecture=amd64) as the guest OS

You could still make this guide work with other setups (possibly with some modifications to the commands and whatnot). 
But if you want to do it the way I did then please feel free to use my setup above.

## Initial Steps
- Open VirtualBox

  ![screenshot](https://i.imgur.com/9El6CZS.png)
  
- Right-click your VM, then click **Settings**

  ![screenshot](https://i.imgur.com/ctfN9kQ.png)
  
- Go to **Shared Folders** section

  ![screenshot](https://i.imgur.com/XrVmNKk.png)
  
- Add a new shared folder

  ![screenshot](https://i.imgur.com/YlUdlGe.png)
  
- On **Add Share** prompt, select the **Folder Path** in your host that you want to be accessible inside your VM. Type `shared` for the **Folder Name**. Make sure that **Read-only** and **Auto-mount** are unchecked and **Mount point** is blank. Then click **OK**.

  ![screenshot](https://i.imgur.com/hNW7T1C.png)
  
- Start your VM

  ![screenshot](https://i.imgur.com/PbTLgts.png)
  
- Once your VM is up and running, go to **Devices** -> **Insert Guest Additions CD image**

  ![screenshot](https://i.imgur.com/RgLw2Jw.png)
  
- Use the following command to mount the CD
  ```
  sudo mkdir /media/cdrom
  sudo mount -t iso9660 /dev/cdrom /media/cdrom
  ```
- Install dependencies for VirtualBox guest additions
  ```
  sudo apt-get update
  sudo apt-get install -y build-essential linux-headers-`uname -r`
  ```
- Run the installation script for the guest additions. Wait until the installation completes.
  ```
  sudo /media/cdrom/./VBoxLinuxAdditions.run
  ```
- Reboot VM
  ```
  sudo shutdown -r now
  ```
- Create `shared` directory in your home
  ```
  mkdir ~/shared
  ```
- Mount the shared folder from the host to your `~/shared` directory
  ```
  sudo mount -t vboxsf shared ~/shared
  ```
- The host folder should now be accessible inside the VM.
  ```
  cd ~/shared
  ```
## Make the mount folder persistent
This directory mount we just made is temporary and it will disappear on next reboot. To make this permanent, we'll set it so that it will mount our `~/shared` directory on system startup

- Edit `fstab` file in `/etc` directory
  ```
  sudo nano /etc/fstab
  ```
- Add the following line to `fstab` (separated by tabs). Make sure to replace `<username>` with your username. Save the file.
  ```
  shared	/home/<username>/shared	vboxsf	defaults	0	0
  ```
- Edit `modules`
  ```
  sudo nano /etc/modules
  ```
- Add the following line to `/etc/modules` and save
  ```
  vboxsf
  ```
- Reboot the VM and log-in again
  ```
  sudo shutdown -r now
  ```
- Go to your home directory and check to see if the directory is highlighted in green. 

  ![screenshot](https://i.imgur.com/hiXgIFP.png)

If it is then congratulations! You successfully linked the directory within your VM with your host folder.

## Bonus: Using shared folders as Apache root directory
How to point apache's web directory to our folder in the host.
- Remove apache's old `html` directory (WARNING! Backup your data if necessary)
  ```
  sudo rm -rf /var/www/html	
  ```
- Add a symbolic link in its place
  ```
  sudo ln -s ~/shared /var/www/html
  ```
*Note: This setup works fine with Windows hosts. But if you are using Linux or Mac as the host then you may have to set appropriate file permissions on your host directory with chmod in order to make it work.*

## Still not working?
If you've followed the steps above and it still doesn't work, please let me know by posting a comment below. Please use the following format and be sure to be as detailed as possible so that I can have enough information to help you out.
```
VirtualBox version: <insert VirtualBox version here>
Host: <OS name> <version>
Guest: <OS name> <version>
Description: <detailed description of the problem>
```
Do note that I'm mostly a Windows user and I'm not that well versed with Linux but I will do my best to help you out.
