# integrated firewall in proxmox
In proxmox there're options to add firewall rules at the Datacenter level, pve level and VMs/Containers also.

In order to use firewall we need to go to the Datacenter, pve and VM/CT and set Firewall->Options-> Edit Firewall Yes

### WARNING!:
IF YOU ENABLE THE FIREWALL AT THE DATACENTER LEVEL WITHOUT CREATING RULES YOU ARE GOING TO GET LOCKED OUT OF THE SERVER, YOU WONT BE ABLE TO ACCESS THE WEB CONSOLE OR SSH INTO IT.

The default input policy is to DROP and output policy is ACCEPT.

If you accidentally enable the firewall and lock yourself out, you'll need to access a terminal on your server and modify the "/etc/pve/firewall/cluster.fw" file to regain access.
```
nano /etc/pve/firewall/cluster.fw
```
Set enable to 0 to disable the firewall and save the file
```
[OPTIONS]
enable: 0 
```
## Add new rule to access the web console
Adding a new rule to the Datacenter that allows tcp traffic on Destination Port 8006 which is the default for the proxmox web console
### Example
Datacenter->Firewall->Add
```
Direction in
Action ACCEPT
Interface vmbr0 (or whatever the name of your proxmox interface is)
Source (blank or a specific IP address if we want)
Destination (blank)
Enable (checked)
Macro (blank)
Protocol tcp
Source port (blank or a specific port)
Dest. port 8006
```
## Add new rule to access to ping the server
### Example
Datacenter->Firewall->Add
```
Direction in
Action ACCEPT
Interface vmbr0 (or whatever the name of your proxmox interface is)
Source (blank or a specific IP address if we want)
Destination (blank)
Enable (checked)
Macro (blank)
Protocol icmp
Source port (blank or a specific port)
ICMP type (blank or type)
```
## Add new rule to SSH into the server
### Example
pve->Firewall->Add
```
Direction in
Action ACCEPT
Interface vmbr0 (or whatever the name of your proxmox interface is)
Source (blank or a specific IP address if we want)
Destination (blank)
Enable (checked)
Macro (SSH)
...
```
## Add new rule to SSH into a VM
### Example
VM->Firewall->Add
```
Direction in
Action ACCEPT
Interface  (blank)
Source (blank or a specific IP address if we want)
Destination (blank)
Enable (checked)
Macro (SSH)
...
```