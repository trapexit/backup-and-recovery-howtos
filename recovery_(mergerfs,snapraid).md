# Recovery w/ MergerFS & SnapRaid

This assumes a setup similar to what is described [here](backup_(mergerfs,snapraid).md).

### Full drive recovery

#### Initial state

* 4 Pooled Drives
  * /mnt/data0
  * /mnt/data1
  * /mnt/data2
  * /mnt/data3
* Pool mountpoint: /mnt/pool
* Parity drive: /mnt/parity0
* Failed drive: /mnt/data1
* New drive: /mnt/data4

#### Restore Procedure
1. Read the [SnapRaid manual](http://www.snapraid.it/manual)
2. Modify `/etc/snapraid.conf` to include the new data drive:  
`disk disk1 /mnt/data1`  
becomes  
`disk disk1 /mnt/data4`
3. Fix the drive (will take a long time):  
`$ snapraid -d disk1 -l /tmp/snapraid-disk1-fix.log fix`
4. Check that the data is OK (will take a long time, skippable):  
`$ snapraid -d disk1 -a check`
5. Resync with new drive:  
`$ snapraid sync`
6. Remove old drive from mergerfs pool:  
`$ sudo xattr -w user.mergerfs.srcmounts -/mnt/data1 /mnt/pool/.mergerfs`
7. Add new drive to mergerfs pool:  
`$ sudo xattr -w user.mergerfs.srcmounts +/mnt/data4 /mnt/pool/.mergerfs`
