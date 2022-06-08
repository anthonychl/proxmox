# user management in proxmox
There are two realms when it comes to users in proxmox, PAM(standard linux authentication) users and PVE(proxmox authentication) users. Both can use proxmox, the difference is where those users are created and where the information for those users are stored. 

## adding a user
- PAM user:
    Click Datacenter -> Users -> Add
    Fill User name, Select Realm 'Linux PAM standard authentication', and other optional data like Group, Expiration date, etc.
    Click Add

    This will add a user to the proxmox GUI but if we check /etc/passd on our pve we realize it has not been created on the system so we need to add it:
    ```
        root@pve:~# adduser anthony
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
        Is the information correct? [Y/n] 
        root@pve:~# cat /etc/passwd | grep anthony
        anthony:x:1000:1000:,,,:/home/anthony:/bin/bash
    ```
    If we log out from root and log in as our new user, at this time we wont be able to do much or even see the VMs or CTs yet, just the pve, cause the user doesnt have permission.

- PVE user:
    Click Datacenter -> Users -> Add
    Fill User name, Select Realm 'Proxmox VE authentication server', Password(in this case it does prompt us to type one unlike with PAM) and other optional data like Group, Expiration date, etc.
    Click Add

    If we log out from root and log in as our new user, at this time we wont be able to do much or even see the VMs or CTs yet, just the pve, cause the user doesnt have permission.

If both users have the same name, when you log in, make sure you select the Realm of the user, PAM or PVE.

The PVE user exists in Proxmox to be able to manage the proxmox system itself(e.g. create VMs) from the GUI. PAM users can also manage the system from the shell(e.g. SSH into the server).

## adding a group
Click Datacenter -> Groups -> Create, fill group name(e.g. admins) and optional comments

## adding permissions to a group
Click Datacenter -> Permissions -> Add -> Group Permissions, select the permissions you want to give to the group.

for example Path: / , Group: admins , Role: Administrator.

Click Add 

Remember to restrict permissions as much as possible,depending on the group or user needs.

## assigning a group to a user
Click Datacenter -> Users -> anthony (Realm PVE) -> Edit -> Groups: admins

You can repeat the same steps for your PAM user








