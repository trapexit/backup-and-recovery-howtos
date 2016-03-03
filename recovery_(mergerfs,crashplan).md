# Recovery using MergerFS & CrashPlan

This assumes a setup similar to what is described [here](backup_(mergerfs,crashplan).md).

### Whole drive recovery

#### Initial state

* 4 Pooled Drives
  * /mnt/1TB-Seagate-0
  * /mnt/2TB-WDC-0
  * /mnt/2TB-WDC-1
  * /mnt/3TB-Seagate-0
* Pool mountpoint: /mnt/pool
* Failed drive: /mnt/2TB-WDC-0
* New drive: /mnt/8TB-Seagate-0

#### Restore Procedure

1. Remove failed drive from mergerfs pool  
`$ sudo xattr -w user.mergerfs.srcmounts -/mnt/2TB-WDC-0 /mnt/pool/.mergerfs`
2. [Format 8TB-Seagate-0](backup_(mergerfs,crashplan).md#1-format-the-drives)
3. Add `8TB-Seagate-0` to fstab
4. Mount  
`$ sudo mount /mnt/8TB-Seagate-0`
5. Add new drive to mergerfs pool  
`$ sudo xattr -w user.mergerfs.srcmounts +/mnt/8TB-Seagate-0 /mnt/pool/.mergerfs`
6. Open the CrashPlan GUI
7. Select Restore from the left panel
8. Click on `most recent` and change the date/time to be before the drive died
8. Navigate in the file viewer to `/mnt/2TB-WDC-0` and select the directory
9. Click `Desktop` till `a folder (Desktop)` appears. Click on `(Desktop)` and select `/mnt/8TB-Seagate-0`
10. Click `Restore`
11. Wait
