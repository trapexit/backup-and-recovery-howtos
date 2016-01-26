# EXT4 Maintenance

### File Fragmentation

Over time a filesystem can become fragmented. To defrag a EXT4 filesystem use the tool `e4defrag`. It can be used while the filesystem is mounted. This should be run once in a while. Perhaps monthly via a cron job.


```
root@host:~# e4defrag
Usage   : e4defrag [-v] file...| directory...| device...
        : e4defrag  -c  file...| directory...| device...
root@host:~# e4defrag -c /mnt/<drive> # Will show fragmented files and provide a score 
root@host:~# ls -1 /mnt | xargs -n1 e4defrag -v # Will defrag all EXT4 drives mounted in /mnt

### Directory Optimization

Directories can, over time, become in need of reindexing, sorting, or compression. This helps with performance but isn't something that can really be done at runtime. The tool `fsck.ext4` allows you to request directory optimization.

With the filesystem unmounted (booted into safemode or from a USB stick / CDROM):

```
# fsck.ext4 -f -D <device>
e2fsck 1.42.9 (4-Feb-2014)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 3A: Optimizing directories
Pass 4: Checking reference counts
Pass 5: Checking group summary information

DriveLabel: ***** FILE SYSTEM WAS MODIFIED *****
DriveLabel: 1076/238496 files (0.0% non-contiguous), 205416408/244190645 blocks
```

This should be run after file defrag'ing.
