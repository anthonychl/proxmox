# creating container templates
## 1- update the apt database
```
anthony@webserver-ct:~$ sudo apt update && sudo apt dist-upgrade
```
## 2- clean apt database and remove orphaned or unused packages
```
anthony@webserver-ct:~$ sudo apt clean
[sudo] password for anthony: 
anthony@webserver-ct:~$ sudo apt autoremove
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```
## 3- delete ssh_host keys, we dont want our template to duplicate ssh_host keys on multiple CTs
```
anthony@webserver-ct:~$ cd /etc/ssh
anthony@webserver-ct:/etc/ssh$ ls
moduli         ssh_host_dsa_key        ssh_host_ed25519_key.pub
ssh_config     ssh_host_dsa_key.pub    ssh_host_rsa_key
ssh_config.d   ssh_host_ecdsa_key      ssh_host_rsa_key.pub
sshd_config    ssh_host_ecdsa_key.pub  ssh_import_id
sshd_config.d  ssh_host_ed25519_key
anthony@webserver-ct:/etc/ssh$ sudo rm ssh_host_*
[sudo] password for anthony: 
anthony@webserver-ct:/etc/ssh$ ls
moduli  ssh_config  ssh_config.d  sshd_config  sshd_config.d  ssh_import_id
```
## 4- delete the contents of the /etc/machine-id file
the location of this file may vary or not be used at all depending on the distro, on ubuntu is /etc/machine-id, note that we empty the file not deleting the file itself
```
anthony@webserver-ct:~$ cat /etc/machine-id
f16ace04b8544f01b97d6a5e9043df8a
anthony@webserver-ct:~$ sudo truncate -s 0 /etc/machine-id 
anthony@webserver-ct:~$ cat /etc/machine-id
anthony@webserver-ct:~$ 

```
## 5- poweroff the CT

## 6- in proxmox, right-click the CT and convert to template

## 7- create a CT from template
  - right-click the template, click 'Clone'
  - select the Target Node (pve), select Mode (Linked or Full clone( Full clone is better)), type a Name and CT id, Target Storage is lvm-thin in my case, and click 'clone'

## 8- ssh into the CTs, configure the /etc/hostname and /etc/hosts files to the proper hostname if necessary
  - if you cant ssh into the new CTs:
    check if the ssh service failed to run, in my case reinstalling openssh-server solved it
    ```
    sudo apt reinstall openssh-server
    ```
    ```
    anthony@webserver-ct-1:~$ systemctl status sshd
    * ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: inactive (dead) since Fri 2022-06-03 02:45:26 UTC; 9s ago
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 626 ExecStart=/usr/sbin/sshd -D $SSHD_OPTS (code=exited, status=0/SUCCESS)
    Main PID: 626 (code=exited, status=0/SUCCESS)
        CPU: 49ms

    anthony@webserver-ct-1:~$ sudo systemctl start sshd

    anthony@webserver-ct-1:~$ systemctl status sshd
    * ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2022-06-03 02:45:42 UTC; 2s ago
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 676 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
    Main PID: 677 (sshd)
      Tasks: 1 (limit: 8192)
     Memory: 1.7M
        CPU: 57ms
     CGroup: /system.slice/ssh.service
             `-677 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"
    ```