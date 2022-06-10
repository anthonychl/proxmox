# snapshots and backups in proxmox
## VMs
If you click on a VM, you can see in the panel options for both snapshots and backups. A backup is completely separate from the VM it is a full clone of the VM disk and it can be copied to another storage media. A snapshot is part of the VM disk and not a clone of the disk like a backup is.
- snapshots

    Click Snapshots -> Take snapshot, type a name, a comment if you want and select whether or not you want to save the state of the RAM too.

    If we want to revert back to a previos state
    Click Snapshots -> select the snapshot -> Rollback, and then
    start the VM (if we didnt save the RAM state).

- backups

    Click Backups -> Backup now, we select Storage (local or other location), Mode (snapshot, suspend, stop), email, notes, Compression (LZO fast, ZSTD fast and good, GZIP good) -> click Backup

    Stop mode shuts down the VM and then creates a backup this will have the best consistency, less chance of data corruption. Suspend mode is not really recommended as it results in the greatest amount of downtime, it puts the VM into suspend mode and then creates the backup. Snapshot mode results in the least amount of downtime, it will capture data blocks while the VM is running, if we have the qemu agent installed on the VM that will increase the consistency of the backup.

    If we want to restore from a backup we need to shutdown the VM if its running, then click Backups -> select the backup -> Restore

## Automating backups
We can add a backup job by clicking Datacenter -> Backup -> Add 

There we can select the node or nodes, which VMs, mode(snapshot,suspend,stop), compression, storage, and the schedule.
