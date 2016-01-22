# Recovery
---

### Symptom: full drive failure
If a drive completely dies you're in luck. At least with regard to the amount of work needed to recover your data. You can recover with either SnapRaid or CrashPlan.

#### CrashPlan
1. Goto "Restore"
2. Select "Show deleted files"
3. Navigate to the mountpoint of the drive which died
4. If you have a new drive of equal or greater size and wish to restore to it select the directory for the new drive (or set to 'original location' if mounted in the same location.) If you want to take advantage of MergerFS's policies then select `/mnt/pool`. Note that if you are using a policy like `epmfs` then the new drive won't be used because it is empty.
5. Wait for your data to finish downloading.

#### SnapRaid
1. Read the [SnapRaid manual](http://www.snapraid.it/manual)
2. If the mountpoint for the new drive is different from that which died modify `snapraid.conf` to have it point to the new mount.
3. Run `snapraid -d NameOfDrive -l /tmp/snapraid-fix.log fix`
4. Wait for your data to be restored
5. If for some reason a file is not recoverable it will be renamed with an extension of ".unrecoverable"
6. To confirm the data is good you can run `snapraid -d NameOfDrive check`
7. Run `snapraid sync` to resync the array
