# Basic qm and pct commands
We can use the command line by accessing the pve directly or via ssh, as root. The following are basic commands to handle the VMs and CTs.

## VM commands

`qm` command, which stands for qemu manager, is the command we'll use to manage VMs. You can check ` man qm ` for more info.

### ` qm list `
Lists all the VMs.
```
root@pve:~# qm list
      VMID NAME                 STATUS     MEM(MB)    BOOTDISK(GB) PID       
       100 webserver            stopped    1024              16.00 0         
       101 webserver-1          stopped    1024              16.00 0         
       102 webserver-2          stopped    1024              16.00 0    

```
### ` qm start VMID `
Start a VM by its ID number
```
root@pve:~# qm start 101
generating cloud-init ISO
```
By the previous example of ` qm list ` we dont have a way to know which of the listed VMs is a template, if we try to start one by mistake we get this error message
```
root@pve:~# qm start 100
you can't start a vm if it's a template
```
### ` qm shutdown VMID `
Shutdown a VM by its ID number
```
root@pve:~# qm shutdown 101
root@pve:~# 
```
### ` qm reboot VMID `
Reboot a VM by its ID number
```
root@pve:~# qm reboot 101
root@pve:~# 
```
These next 2 commands are potentially destructive, but we can use them when a VM is misbehaving and wont shutdown or reboot gracefully.

### ` qm reset VMID `
Reset a VM by its ID number. Unlike reboot, reset is abrupt.
```
root@pve:~# qm reset 101
root@pve:~# 
```

### ` qm stop VMID `
Stop a VM by its ID number. Unlike shutdown, stop is abrupt.
```
root@pve:~# qm stop 101
root@pve:~# 
```
### Configuring VM options using  ` qm set --option value VMID `

Set start on boot to NO (0) or YES (1)
```
root@pve:~# qm set --onboot 0 101
update VM 101: -onboot 0

root@pve:~# qm set --onboot 1 101
update VM 101: -onboot 1
```
### ` qm config VMID `
Shows information about the VM and options we can modify
```
root@pve:~# qm config 101
agent: 1
boot: order=scsi0;ide2;net0;ide0
cipassword: **********
ciuser: anthony
cores: 1
ide0: local-lvm:vm-101-cloudinit,media=cdrom,size=4M
ide2: none,media=cdrom
memory: 1024
meta: creation-qemu=6.2.0,ctime=1654044025
name: webserver-1
net0: virtio=CA:E0:F0:66:7F:E9,bridge=vmbr0,firewall=1
numa: 0
onboot: 1
ostype: l26
scsi0: local-lvm:vm-101-disk-0,discard=on,size=16G
scsihw: virtio-scsi-pci
smbios1: uuid=59ef4643-a29b-4e67-b8c6-e2e14983a79f
sockets: 1
vmgenid: b89ebfd8-a338-4567-9830-f1e5ccb8d669
```
Back to ` qm set ` now that we saw some options.

Modifying memory:
```
root@pve:~# qm set --memory 2048 101
update VM 101: -memory 2048
```

## CT commands
` pct command ` which stands for proxmox container toolkit.

### ` pct list `
Lists all the CTs. Even though it says VMID is the CT ID number.
```
root@pve:~# pct list
VMID       Status     Lock         Name                
103        stopped                 webserver-ct        
104        stopped                 webserver-ct-1      
105        stopped                 webserver-ct-2
```
### ` pct config VMID `
Shows information/options about the CT we can modify
```
root@pve:~# pct config 104
arch: amd64
cores: 1
features: nesting=1
hostname: webserver-ct-1
memory: 512
net0: name=eth0,bridge=vmbr0,firewall=1,hwaddr=E2:A0:B2:B2:69:13,ip=dhcp,ip6=dhcp,type=veth
onboot: 1
ostype: ubuntu
rootfs: local-lvm:vm-104-disk-0,size=8G
swap: 512
unprivileged: 1
```

### ` pct start VMID `
Starting CTs by ID number
```
root@pve:~# pct start 103
you can't start a CT if it's a template

root@pve:~# pct start 104
root@pve:~#
```
### ` pct shutdown VMID `
Shutdown a CT by its ID number
```
root@pve:~# pct shutdown 104
root@pve:~# 
```
### ` pct reboot VMID `
Reboot a CT by its ID number
```
root@pve:~# pct reboot 104
root@pve:~# 
```

### Opening a shell in the container with ` pct enter VMID `
```
root@pve:~# pct enter 104
root@webserver-ct-1:~# 
```
### Configuring VM options using  ` pct set VMID -option value `
Notice in the syntax `-option` here uses 1 dash(-) instead of 2 like qm

Set start on boot to NO (0) or YES (1)
```
root@pve:~# pct set 104 -onboot 0 
root@pve:~#

root@pve:~# pct set 104 -onboot 1
root@pve:~#
```
