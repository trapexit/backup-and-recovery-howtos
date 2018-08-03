# Backup, Recovery, & Maintenance Howtos

### Backup Howtos
* [MergerFS & CrashPlan](docs/backup_(mergerfs,crashplan).md):
  * pooled storage w/ mergerfs
  * offsite backup via CrashPlan
  * N drives used for raw storage
  * Recovery of drives or individual files (versioned)
* [MergerFS, CrashPlan, & SnapRaid](docs/backup_(mergerfs,crashplan,snapraid).md):
  * pooled storage w/ mergerfs
  * offsite backup via CrashPlan
  * local backup and corruption detection/correction via SnapRaid
  * 1+ drives dedicated to parity (must be as large as the largest storage drive)
  * N drives for raw storage
* [ZFS & CrashPlan](docs/backup_(zfs,crashplan).md) (TBD):
  * pooled storage, corruption detection/correcton, redundency w/ ZFS
  * offsite backup via CrashPlan
* [BTRFS & CrashPlan](docs/backup_(btrfs,crashplan).md):
  * pooled storage, corruption detection/correcton, redundency w/ BTRFS
  * offsite backup via CrashPlan

### Recovery Howtos
* [MergerFS](docs/recovery_(mergerfs).md)
* [MergerFS & CrashPlan](docs/recovery_(mergerfs,crashplan).md)
* [MergerFS & SnapRaid](docs/recovery_(mergerfs,snapraid).md)
* [MergerFS, CrashPlan, & SnapRaid](docs/recovery_(mergerfs,crashplan,snapraid).md)
* [EXT4](docs/recovery_(ext4).md)
* [ZFS](docs/recovery_(zfs).md)
* [BTRFS](docs/recovery_(btrfs).md)
* [MBR/GPT](docs/recovery_(mbr,gpt).md)

### Maintenance Howtos
* [EXT4](docs/maintenance_(ext4).md)
* [Btrfs](docs/maintenance_(btrfs).md)
* [MergerFS](docs/maintenance_(mergerfs).md)
* [SnapRaid](docs/maintenance_(snapraid).md)

### Setup Howtos
* [Storage devices](docs/setup_(storage_device).md)
* [EXT4](docs/setup_(ext4).md)
* [/etc/fstab](docs/setup_(fstab).md)
* [MergerFS](docs/setup_(mergerfs).md)
* [SnapRaid](docs/setup_(snapraid).md)
* [CrashPlan](docs/setup_(crashplan).md)
