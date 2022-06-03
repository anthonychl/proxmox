# creating containers in proxmox
Proxmox allowes to create the LXC( Linux containers, pronounced lexee) container type. LXC are more VM-like, in the sense that it saves it state automatically if powered-off, unlike other container types.

## 1- download a template
    - click on local(pve) -> CT Templates -> Templates
    - select a ubuntu template (or whichever one you need)

## 2- create the container
    - click Create CT
    - General tab: select node, set password(for root account), hostname
    - Template tab: select the template we downloaded
    - Disks, CPU, Memory tabs: set the resources
    - Network tab: set the IP staticly or in my case, I selected DHCP
    - DNS tab: use host settings

## 3- Set container Options to 'start at boot  Yes' and keep 'unpriviledged container Yes' 

## 4- if you plan to ssh into the container, doing it through the root account might result in error so we need to add a user, and add it to the sudo group.
Make sure ssh is installed and running
```
root@webserver-ct:~# systemctl status sshd
* ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: inactive (dead)
       Docs: man:sshd(8)
             man:sshd_config(5)
root@webserver-ct:~# systemctl start sshd
root@webserver-ct:~# systemctl status sshd
* ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2022-06-03 01:48:03 UTC; 2s ago
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 445 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 446 (sshd)
      Tasks: 1 (limit: 8192)
     Memory: 2.8M
        CPU: 59ms
     CGroup: /system.slice/ssh.service
             `-446 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"

```
create new user
```
root@webserver-ct:~# adduser anthony
Adding user `anthony' ...
Adding new group `anthony' (1000) ...
Adding new user `anthony' (1000) with group `anthony' ...
Creating home directory `/home/anthony' ...
Copying files from `/etc/skel' ...
New password: 
Retype new password: 
passwd: password updated successfully
Changing the user information for anthony
Enter the new value, or press ENTER for the default
        Full Name []: 
        Room Number []: 
        Work Phone []: 
        Home Phone []: 
        Other []: 
Is the information correct? [Y/n] y
root@webserver-ct:~#
```
add new user to sudo group
```
root@webserver-ct:~# usermod -aG sudo anthony
```
ssh into the container
```
anthony@anthony-HP-Pavilion:~$ ssh anthony@10.0.0.88
The authenticity of host '10.0.0.88 (10.0.0.88)' can't be established.
ECDSA key fingerprint is SHA256:3CEkoSsZ6kJhb9UVg1p9ZmYt7jcHAo1yvqidvLbNGkE.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.0.0.88' (ECDSA) to the list of known hosts.
anthony@10.0.0.88's password: 
Welcome to Ubuntu 22.04 LTS (GNU/Linux 5.15.30-2-pve x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
```

