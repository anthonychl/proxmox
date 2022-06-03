# creating a VM template in proxmox
## 1- check cloud-init is installed on the VM
```
anthony@webserver:~$ apt search cloud-init
Sorting... Done
Full Text Search... Done
cloud-init/jammy,now 22.1-14-g2e17a0d6-0ubuntu1~22.04.5 all [installed,automatic]
  initialization and customization tool for cloud instances
```
## 2- delete ssh_host keys, we dont want our template to duplicate ssh_host keys on multiple VMs
```
anthony@webserver:~$ cd /etc/ssh
anthony@webserver:/etc/ssh$ ls
moduli         ssh_host_dsa_key        ssh_host_ed25519_key.pub
ssh_config     ssh_host_dsa_key.pub    ssh_host_rsa_key
ssh_config.d   ssh_host_ecdsa_key      ssh_host_rsa_key.pub
sshd_config    ssh_host_ecdsa_key.pub  ssh_import_id
sshd_config.d  ssh_host_ed25519_key
anthony@webserver:/etc/ssh$ sudo rm ssh_host_*
[sudo] password for anthony: 
anthony@webserver:/etc/ssh$ ls
moduli  ssh_config  ssh_config.d  sshd_config  sshd_config.d  ssh_import_id
```
## 3- delete the contents of the /etc/machine-id file
the location of this file may vary or not be used at all depending on the distro, on ubuntu is /etc/machine-id, note that we empty the file not deleting the file itself
```
anthony@webserver:~$ cat /etc/machine-id
f16ace04b8544f01b97d6a5e9043df8a
anthony@webserver:~$ sudo truncate -s 0 /etc/machine-id 
anthony@webserver:~$ cat /etc/machine-id
anthony@webserver:~$ 

```
## 4- check the symbolic link to the machine-id file exists or create it
```
anthony@webserver:~$ ls -l /var/lib/dbus/machine-id 
lrwxrwxrwx 1 root root 15 Jun  1 01:16 /var/lib/dbus/machine-id -> /etc/machine-id
```
or
```
anthony@webserver:~$ sudo ln -s /etc/machine-id /var/lib/dbus/machine-id  
anthony@webserver:~$ ls -l /var/lib/dbus/machine-id 
lrwxrwxrwx 1 root root 15 Jun  1 01:16 /var/lib/dbus/machine-id -> /etc/machine-id

```
## 5- clean apt database and remove orphaned or unused packages
```
anthony@webserver:~$ sudo apt clean
[sudo] password for anthony: 
anthony@webserver:~$ sudo apt autoremove
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```
## 6- If there's any packages or configuration files you need every VM to have, install it.
for instance: qemu-guest-agent
```
anthony@webserver:~$ sudo apt install qemu-guest-agent 

```
## 7- poweroff the VM

## 8- in proxmox, right-click the VM and convert to template
this process with destroy the VM itself, beware

## 9- in proxmox, select the template, go to 'Hardware': 
  - edit CD/DVD drive and select 'do not use any media'
  - add Cloud-init drice and select 'local-lvm'

## 10- in proxmox, select the template, go to 'Cloud-init': 
  - edit User to add a default username
  - edit password for that User
  - click 'regenerate image'

## 11- create a VM
  - right-click the template, click 'Clone'
  - select the Target Node (pve), select Mode (Linked or Full clone( Full clone is better)), type a Name and VM id, Target Storage is lvm-thin in my case, and click 'clone'

## 12- ssh into the VMs, configure the /etc/hostname and /etc/hosts files to the proper hostname
  - if you cant ssh into the new VMs:
    check if the ssh service failed to run, in my case reinstalling openssh-server solved it
    ```
    sudo apt reinstall openssh-server
    ```
    



