# Backup w/ MergerFS & CrashPlan
This document is intended to provide an example of how to setup a system to store large amounts of mostly write once / read often data with reasonably simple backup and recovery at a decent price.

## Technologies
* [GNU/Linux (Ubuntu 16.04)](http://ubuntu.com)
* [mergerfs](http://github.com/trapexit/mergerfs)
* [CrashPlan](https://www.crashplan.com)

#### What and Why?
There are many technologies in the storage space and the ease of use, cost, and reliability vary greatly. Here we'll attempt to articulate why these particular technologies were used versus others available.

##### mergerfs
---
###### What?
MergerFS is a union filesystem which can make several drives look as if it's merged (or pooled) together as one. It also allows for policies to be applied to different filesystem functions which make it very nice for individuals storing large amounts of media.

###### Why?
* https://github.com/trapexit/mergerfs#faq
* **Why not mhddfs?**  
mhddfs is no longer maintained and has some known stability and security issues (see below). MergerFS provides a superset of mhddfs' features and should offer the same or maybe better performance.
* **Why not aufs?**
While aufs can offer better peak performance mergerfs offers more configurability and is generally easier to use. mergerfs however doesn't offer the same overlay features (which tends to result in whiteout files being left around the underlying filesystems.)
* **Why not LVM/ZFS/BTRFS/RAID0 drive concatenation/striping?**
With simple JBOD / drive concatenation / stripping / RAID0 a single drive failure will lead to full pool failure. mergerfs performs a similar behavior without the catastrophic failure and general lack of recovery. Drives can fail and all other data will continue to be accessable.

##### CrashPlan
---
###### What?
A proprietary cross-platform backup solution which offers unlimited backups, data deduplication, compression, and encryption via an easy to use GUI.

###### Why?
While the current consumer CrashPlan client (version 4.8.0) is Java based and with large collections of files can be CPU and memory heavy the price for unlimited, cross-platform backup makes it a very nice service. Especially given it can be used without Code42's cloud service so it's safe to use without a service contract. The downside is that it's completely proprietary and lacks features powerusers may want. Some of those flaws however can be worked around.

### Hardware
---
Let's assume we have the following hardware setup.
* 6 drives
  * boot/os drive: /dev/sda
  * data drives: /dev/sd{b,c,d,e}; labeled DATA-DRIVE{0,1,2,3}
* The sizes of the data drives are unimportant.

## Setup
---
#### 1. Test the drives
New drives should probably go through a burnin test prior to being used. Note however that such a test on modern large drives can take a very long time (days). If you are using preexisting drives with data already on them you can skip this step.

Read the [storage device setup guide](setup_(storage_device).md) for specifics.

#### 2. Format the drives
Neither MergerFS or CrashPlan care about the filesystem type. EXT4 is probably the best filesystem right now for this setup. Especially given many recovery tools understand EXT{2,3,4} filesystems. BTRFS is also a possibility though data recovery tools are very immature at the moment.

* [EXT4 setup guide](setup_(ext4).md)

#### 3. Create mount locations
```
# mkdir /mnt/data{0,1,2,3}
# mkdir /storage
```

#### 4. Add to /etc/fstab
```
# <file system>   <mount point>   <type> <options>                             <dump>  <pass>
LABEL=DATA-DRIVE0 /mnt/data0      auto   defaults,nobootwait,errors=remount-ro 0       2
LABEL=DATA-DRIVE1 /mnt/data1      auto   defaults,nobootwait,errors=remount-ro 0       2
LABEL=DATA-DRIVE2 /mnt/data2      auto   defaults,nobootwait,errors=remount-ro 0       2
LABEL=DATA-DRIVE3 /mnt/data3      auto   defaults,nobootwait,errors=remount-ro 0       2
```

Read the [fstab setup guid](setup_(fstab).md) for details on the options.

#### 5. Prepare mergerfs
Add entry to `/etc/fstab`
```
/mnt/data*  /storage  fuse.mergerfs  defaults,allow_other,direct_io,moveonenospc=true  0  0
```

Look at the [mergerfs readme](https://github.com/trapexit/mergerfs#options) for other options and further details.

#### 6. Prepare CrashPlan

Follow the [CrashPlan setup guide](setup_(crashplan).md). Create a "backup set" for each type of data you wish to backup (photos, music, etc.) and for each source drive (/mnt/data{0,1,2,3}).
