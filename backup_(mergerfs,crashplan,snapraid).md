# Backup and Recovery
This document is intended to provide an example of how to setup a system to store large amounts of mostly write once / read often data with reasonably simple backup and recovery at a decent price.

## Technologies
* [GNU/Linux (Ubuntu 14.04.3)](http://ubuntu.com)
* [mergerfs](http://github.com/trapexit/mergerfs)
* [CrashPlan](http://www.code42.com/crashplan)
* [snapraid](http://www.snapraid.it) (optional)

#### What and Why?
There are many technologies in the storage space and the ease of use, cost, and reliability vary greatly. Here we'll attempt to articulate why these particular technologies were used versus others available.

##### mergerfs
---
###### What?
MergerFS is a union filesystem which can make several drives look as if it's merged (or pooled) together as one. It also allows for policies to be applied to different filesystem functions which make it very nice for individuals storing large amounts of media.
###### Why?
* **Why not AUFS?**  
AUFS is kernel based and any updates require updating the kernel modules which isn't trivial for the average user. It also is less flexible with regard to the policies it offers and leaves whiteout files around which can be annoying. Throughput will most likely be higher versus a FUSE solution but new features which may be coming to FUSE should minimize AUFS's advantage.
* **Why not MHDDFS?**  
It's no longer actively maintained, less secure, and lacks the flexibility.
* **Why not hardware or software RAID0?**  
Should a single drive fail the whole array fails. It's preferable that only the data on the drive that failed needs to be recovered.
* **Why not ZFS or BTRFS drive concatenation?**  
ZFS and BTRFS offer a decent amount of functionality but is not always easy to use and is in some ways overkill for a dumping ground for media when used with more consumer level services.

##### CrashPlan
---
###### What?
A proprietary cross-platform backup solution which offers unlimited backups, data deduplication, compression, and encryption via an easy to use GUI.
###### Why?
While the current consumer CrashPlan client (version 4.5.2) is Java based and with large collections of files can be CPU and memory heavy the price for unlimited, cross-platform backup makes it a very nice service. Especially given it can be used without Code42's cloud service so it's safe to use without a service contract. The downside is that it's completely proprietary and lacks features powerusers may want. Some of those flaws however can be worked around.

##### SnapRaid
---
###### What?
A backup program for disk arrays. Stores parity and metadata about files which allows for recovery of failed drives and corrupted data.
###### Why?
* **Why use SnapRaid with CrashPlan?**  
It's not necessary but gives you immediate recovery of a drive's data (this can be worked around) and discovery of corrupted data. CrashPlan would allow you to recover from corrupted data but not discover it became corrupted. An alternative would be create checksums for your files and regularly check them.
* **Why not RAID{1,5,6,Z}?**  
SnapRaid provides backup in a way that allows easy recovery of lost drives and can be replaced at any time without affecting the existing setup. It can be used in addition to another backup solution such as CrashPlan to ensure multiple levels of backup and with a union filesystem like mergerfs to provide a single view of the disk pool.
* **Why not ZFS or BTRFS?**  
While ZFS and BTRFS provide features which could be used instead of SnapRaid (such as snapshotting, data recovery, and corruption detection and fixing) they are not very accessible to the average user and still in development in the case of BTRFS. Recovery tools are also not as readily available.

### Hardware
---
Let's assume we have the following hardware setup.
* 6 drives
  * boot/os drive: /dev/sda
  * snapraid parity drive: /dev/sdb
  * data drives: /dev/sd{c,d,e,f}
* Sizes of the drives are uniform. NOTE: SnapRaid requires that the parity drive be **at least** as large as the amount of data stored on a single drive within the pool.

## Install
---

TBD

## Setup
---
#### 1. Format the drives
Neither MergerFS or CrashPlan doesn't care about the filesystem however SnapRaid does. EXT4 is probably the best overall filesystem right now for this setup. Especially given many recovery tools understand EXT{2,3,4} filesystems.
```
# mkfs.ext4 -m 0 -T largefile4 /dev/sd{b,c,d,e,f}
```
The `-m 0` is to keep mkfs.ext4 from reserving capacity for root which means you'll be able to use as much of the disk as possible. `-T largefile4` is optional but useful when you expect to have fewer large files versus many small or average size files. It saves some space. Valid options for `-T` and what they mean are found in the `fs_types` section of `/etc/mke2fs.conf`.

We format the raw drives rather than the creating partitions for the following reasons.

1. The partition is mostly useless to us since we plan on using the entire drive
2. We no longer need to worry about MBR vs GPT
3. We no longer need to worry about block alignment.
4. When a 512e/4k drive is moved between a native SATA controller and a USB SATA converter there won't be partition block misalignment. Often USB adapters will report 4k/4k to the OS while the drive will report 512/4k causing the OS to not be able to find the paritions or filesystems. This can be fixed but no tools exist to do the procedure automatically.

#### 2. Create mount locations
```
# mkdir /mnt/parity0
# mkdir /mnt/data{0,1,2,3}
# mkdir /mnt/pool
```

#### 3. Add to /etc/fstab
```
# <file system> <mount point>   <type>  <options>                        <dump>  <pass>
/dev/sdb        /mnt/parity0     auto    defaults,users,errors=remount-ro 0       2
/dev/sdc        /mnt/data0       auto    defaults,users,errors=remount-ro 0       2
/dev/sdd        /mnt/data1       auto    defaults,users,errors=remount-ro 0       2
/dev/sde        /mnt/data2       auto    defaults,users,errors=remount-ro 0       2
/dev/sdf        /mnt/data3       auto    defaults,users,errors=remount-ro 0       2
```

#### 4. Prepare mergerfs
Add to `/etc/fstab`
```
/mnt/data*  /mnt/pool  fuse.mergerfs  defaults,allow_other,moveonenospc=true  0  0
```

Look at the [mergerfs readme](http://github.com/trapexit/mergerfs) for other options.

#### 5. Prepare SnapRaid
In `etc/snapraid.conf`:
```
# Parity file
parity /mnt/parity0/snapraid.parity0

# You must have least one copy for each parity file plus one.
content /var/snapraid/snapraid.content
content /mnt/disk0/snapraid/snapraid.content

# List of data drives
disk disk0 /mnt/data0
disk disk1 /mnt/data1
disk disk2 /mnt/data2
disk disk3 /mnt/data3

# Excludes hidden files and directories (uncomment to enable).
nohidden

# Defines files and directories to exclude
# Remember that all the paths are relative at the mount points
# Format: "exclude FILE"
# Format: "exclude DIR/"
# Format: "exclude /PATH/FILE"
# Format: "exclude /PATH/DIR/"
exclude /lost+found/
```

Once SnapRaid is setup you'll need to `sync`.

```
# snapraid sync
```

This will need to done every once in a while to ensure snapraid updates everything. snapraid is not a daemon and won't automatically run sync for you. The easiest thing would be to create a `cron` entry to run `snapraid sync` once a week or at whatever frequency you find necessary.


#### 6. Prepare CrashPlan
1. Open the GUI
2. Goto Settings > Backup
3. Enable "Backup Sets"
4. Create a set for each media type you have (music, movies, photos, etc.) and prioritize them as you see fit. For each set add the appropriate directories from  `/mnt/pool`.
5. In the "Advanced Settings" dialog:  
Data de-duplication: Automatic  
Compression: Off  
Encryption enabled: (up to you)
Watch file system in real-time: unchecked
Back up open files: unchecked  
6. Create a new set with the lowest priority and add:  
/mnt/data0  
/mnt/data1  
/mnt/data2  
/mnt/data3
7. **Optional:** Once all the backup sets are created there is a way to get better upload speed by limiting the amount of deduplication CrashPlan attempts. If you expect to store lots of the same data this probably isn't worth it but if you store large amounts of mostly unique data this will help. Apparently CrashPlan's dedup algorithm does not scale well.  
  
  * Open: `/usr/local/crashplan/conf/my.service.xml`
  * Change `socketSendBufferSize` to `1048576`  
  * Change `socketReceiveBufferSize` to `1048576`  
  * For each `dataDeDupAutoMaxFileSizeForWan` found (excluding for the raw mount set) set the value to `1`. `0` in this case appears to mean `infinite`. 
  * Save the file.
  * Restart the CrashPlan service. (Ubuntu: `sudo service crashplan restart`)

"Watch file system" is turned off is due to the overhead it can have on a system when you have lots of files. Compression is off because most every media file is already compressed.

We add the raw drives as well as the specific merged directories is to handle the case of complete hard drive loss. Should a drive die you simply need to goto the drive directory and restore it to the new drive. This works around the fact that CrashPlan has no way to do a smart restore which will ignore existing files if they are already up to date nor can you filter by just files that have been lost (which will show up as deleted from your merged location).
