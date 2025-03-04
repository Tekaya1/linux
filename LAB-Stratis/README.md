```markdown
# Lab stratis correction Exercise

## Stratis Commands

### Create and List Pools
```bash
stratis pool create stratispool1 /dev/sdb
stratis pool list
```

### Add Data to Pool
```bash
stratis pool add-data stratispool1 /dev/sdc
stratis pool list
```

### List Block Devices
```bash
stratis blockdev list
```

### Create and List Filesystems
```bash
stratis fs create stratispool1 fs1
stratis filesystem list
```

### Mount Filesystem
```bash
mkdir /stratisvol
mount /dev/stratis/stratispool1/fs1 /stratisvol
lsblk
stratis filesystem list
```

### Create Files
```bash
echo 'hello World!' > /stratisvol/file1
stratis filesystem list
dd if=/dev/urandom of=/stratisvol/file2 bs=1M count=2048
stratis filesystem list
```

### Create and List Snapshots
```bash
stratis filesystem snapshot stratispool1 fs1 snapshot1
stratis filesystem list
```

### Remove Files and Mount Snapshots
```bash
rm -rf /stratisvol/file1
cat /stratisvol/file1
mkdir /stratisvol-snap
mount /dev/stratis/stratispool1/fs1 /stratisvol-snap/
stratis filesystem list
mount /dev/stratis/stratispool1/snapshot1 /stratisvol-snap/
```

### Unmount and Destroy Filesystems
```bash
umount -a
stratis fs destroy stratispool1 snapshot1
stratis fs destroy stratispool1 fs1
```

### Destroy Pool
```bash
sudo stratis pool destroy stratispool1
```
## Exercice2 

#### Create and Add Data to Pool
```bash
stratis pool create labpool /dev/sdc
stratis pool add-data labpool /dev/sdd
stratis blockdev list
```

#### Create and List Filesystems
```bash
stratis fs create labpool fs1
stratis filesystem list
```

#### Mount Filesystem
```bash
mkdir /labstratisvol
mount /dev/stratis/labpool/fs1 /labstratisvol
lsblk
```

#### Unmount Filesystem
```bash
umount -a
stratis filesystem list
```

#### Edit fstab and Mount All
```bash
vim /etc/fstab
mount -a
```

#### Create and List Snapshots
```bash
stratis filesystem snapshot labpool fs1 labfs-snap
stratis filesystem list
```
```
```
