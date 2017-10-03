# Recovery w/ MergerFS

### Replacing a drive which still functions

#### Initial state

* 3 Pooled Drives
  * /mnt/data0
  * /mnt/data1
  * /mnt/data2
* Pool mountpoint: /mnt/pool
* Drive to replace: /mnt/data1
* New drive: /mnt/data3

#### Replace Procedure

1. Format drive and mount to /mnt/data3: [setup_(ext4).md](setup_(ext4).md)
2. Add new drive to pool  
`$ sudo xattr -w user.mergerfs.srcmounts +/mnt/data3 /mnt/pool/.mergerfs`  
or  
`$ sudo mergerfs.ctl add path /mnt/data3`
3. Use `rsync` or similar to copy data  
`$ sudo rsync -avHAXS /mnt/data1/ /mnt/data3/`
4. Maybe run rsync again to confirm no files changed on the fly. To be sure data won't change first remove /mnt/data1 but know that the data will then no longer be available in the pool.
5. Remove the old drive  
`$ sudo xattr -w user.mergerfs.srcmounts -/mnt/data1 /mnt/pool/.mergerfs`  
or  
`$ sudo mergerfs.ctl remove path /mnt/data1`
