# Backup, Recovery, & Maintenance Howtos

### Backup Howtos
* [MergerFS & CrashPlan](backup_(mergerfs,crashplan).md):
  * pooled storage w/ mergerfs
  * offsite backup via CrashPlan
  * N drives used for raw storage
  * Recovery of drives or individual files (versioned)
* [MergerFS, CrashPlan, & SnapRaid](backup_(mergerfs,crashplan,snapraid).md):
  * pooled storage w/ mergerfs
  * offsite backup via CrashPlan
  * local backup and corruption detection/correction via SnapRaid
  * 1+ drives dedicated to parity (must be as large as the largest storage drive)
  * N drives for raw storage
* [ZFS & CrashPlan](backup_(zfs,crashplan).md):
  * pooled storage, corruption detection/correcton, redundency w/ ZFS
  * offsite backup via CrashPlan
* [BTRFS & CrashPlan](backup_(btrfs,crashplan).md)
  * pooled storage, corruption detection/correcton, redundency w/ BTRFS
  * offsite backup via CrashPlan

### Recovery Howtos
* [MergerFS & CrashPlan](recovery_(mergerfs,crashplan).md)
* [MergerFS & SnapRaid](recovery_(mergerfs,snapraid).md)
* [MergerFS, CrashPlan, & SnapRaid](recovery_(mergerfs,crashplan,snapraid).md)
* [EXT4](recovery_(ext4).md)
* [ZFS](recovery_(zfs).md)
* [BTRFS](recovery_(btrfs).md)
* [MBR/GPT](recovery_(mbr,gpt).md)

### Maintenance Howtos
* [EXT4](maintenance_(ext4).md)
* [MergerFS](maintenance_(mergerfs).md)
