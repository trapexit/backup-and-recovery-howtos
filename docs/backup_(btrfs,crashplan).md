# Backup w/ BTRFS & CrashPlan
This document is intended to provide an example of how to setup a system to store large amounts of data with with flexibility, reasonably simple backup and recovery at a decent price.

## Technologies
* [GNU/Linux (Ubuntu 17.10.1)](https://ubuntu.com)
* [BTRFS](https://btrfs.wiki.kernel.org)

#### What and Why?
There are many technologies in the storage space and the ease of use, cost, and reliability vary greatly. Here we'll attempt to articulate why these particular technologies were used versus others available.

##### BTRFS
---
###### What?
Btrfs is a modern copy on write (CoW) filesystem for Linux aimed at implementing advanced features while also focusing on fault tolerance, repair and easy administration.
###### Why?
BTRFS will be used for pooling and also for the filesystem. The data model will be RAID5, but BTRFS has the flexbility to switch between data models if needed eg. RAID1 to RAID5/6.
* **Why not EXT4/XFS?**
Can't manage the pooling, LVM (logical volume management), snapshotting, bitrot detection.
* **Why not mergerfs?**
Can't switch between data models and lack of advanced features like snapshots and subvolumes, plus BTRFS can be used for pooling and for the FS at the same time.
* **Why not AUFS/MHDDFS/hardware or software RAID0?**  
[Detailed here.](/docs/backup_(mergerfs,crashplan,snapraid).md#why)
* **Why not ZFS?**  
Can't be easily expanded, not as flexible as BTRFS.

##### CrashPlan
---
[Detailed here.](/docs/backup_(mergerfs,crashplan).md#crashplan)

### Hardware
---
Let's assume we have the following hardware setup.
* 4 drives
  * boot/os drive: /dev/sda
  * data drives: /dev/sd{b,c,d}
* Sizes of the drives are uniform.
NOTE: You can use devices of different sizes, but striped RAID levels (RAID-0/10/5/6) may not use all of the available space on the devices.

## Setup
---
#### 1. Format the drives
`sudo mkfs.btrfs -d raid5 /dev/sdb /dev/sdc /dev/sdd`

The output:
```
btrfs-progs v4.12
See http://btrfs.wiki.kernel.org for more information.

Label:              (null)
UUID:               3a6b5f69-23bc-4698-a945-d06062aa5ff6
Node size:          16384
Sector size:        4096
Filesystem size:    33.00GiB
Block group profiles:
  Data:             RAID5             2.00GiB
  Metadata:         RAID1             1.00GiB
  System:           RAID1             8.00MiB
SSD detected:       no
Incompat features:  extref, raid56, skinny-metadata
Number of devices:  3
Devices:
   ID        SIZE  PATH
    1    11.00GiB  /dev/sdb
    2    11.00GiB  /dev/sdc
    3    11.00GiB  /dev/sdd
```

#### 2. Create mount locations
`sudo mkdir /mnt/storage`

#### 3. Add to /etc/fstab
Use the UUID of the newly created BTRFS.
```
# <file system>                           <mount point>  <type>  <options>  <dump>  <pass>
UUID=3a6b5f69-23bc-4698-a945-d06062aa5ff6 /mnt/storage   btrfs    defaults    0       2

```

#### 4. Prepare CrashPlan
Follow the [CrashPlan setup guide](setup_(crashplan).md). Create a "backup set" for each type of data you wish to backup (phtoos, music, etc.).
