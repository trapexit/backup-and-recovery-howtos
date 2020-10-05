# Recovery w/ MergerFS

### Replacing a drive without downtime
For the situation where you have the ability to have the original drive and the new drive connected at the same time.

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
`$ sudo rsync -avxHAXWES --numeric-ids --info=progress2 /mnt/data1/ /mnt/data3/`
4. Run rsync again to confirm no files changed while the copy happened.
5. Remove the old drive  
`$ sudo xattr -w user.mergerfs.srcmounts -/mnt/data1 /mnt/pool/.mergerfs`  
or  
`$ sudo mergerfs.ctl remove path /mnt/data1`

### Removing a drive without downtime
For the situation where you are removing a drive completely or you are unable to have both the old drive and the new drive connected at the same time but have enough storage in the pool to store the drive's data.

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
2. Create a new, temporary pool without the drive you plan to remove. You can just copy your existing options or if you want the data to be spread out differently you might want to change the create policy.  
`$ mkdir -p /tmp/tmp_pool`  
`$ sudo mergerfs -o <options> /mnt/data0:/mnt/data2 /tmp/tmp_pool`
3. Use `rsync` or similar to copy data from the drive to remove into the temporary pool  
`$ sudo rsync -avxHAXWES --numeric-ids --info=progress2 /mnt/data1/ /tmp/tmp_pool/`
4. Run rsync again to confirm no files changed while the copy happened. 
5. Remove the old drive from the main pool.  
`$ sudo xattr -w user.mergerfs.srcmounts -/mnt/data1 /mnt/pool/.mergerfs`  
or  
`$ sudo mergerfs.ctl remove path /mnt/data1`
6. Add the new drive to the system and add to the pool.
