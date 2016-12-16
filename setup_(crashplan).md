# CrashPlan Setup

### Initial Install
#### Standard Install
* Go to https://www.crashplan.com and download the lastest client
* As root:  
  * `# tar xvfz CrashPlan_<version>_Linux.tgz`
  * `# cd crashplan-install`
  * `# ./install.sh`
  * Follow the instructions. It's generally fine to use the defaults.
* On most systems you should have access to the GUI via your desktop. If it doesn't show up for some reason you can start it via a terminal or command launcher: `/usr/local/bin/CrashPlanDesktop`
* The installer will setup CrashPlan to start automatically when your system boots.

#### Docker Install
There are several docker images up on [DockerHub](http://hub.docker.com). Some use only the non-gui backup engine while a few use software such as NoVNC to provide a way to more easily access the GUI. One I've had success with is [gfjardim/crashplan](https://hub.docker.com/r/gfjardim/crashplan/).

```
$ docker run -d --name=crashplan \
  --net=bridge \
  --restart=unless-stopped \
  -p 4242:4242 \
  -p 4243:4243 \
  -p 4280:4280 \
  -v /mnt:/mnt \
  -v /path/to/storage:/storage \
  gfjardim/crashplan
```

To connect to the GUI head to http://yourserversip:4280/vnc.html

### General Setup
1. Open GUI
2. Goto: Settings > Backup
3. Enable "Backup Sets"
4. Create a set for each media type you have (music, movies, photos, etc.) and prioritize as you see fit. If you are using a pooling / union filesystem such as [mergerfs](setup_(mergerfs).md) then you should be adding the merged paths.
5. In the "Frequency and versions" dialog:  
Move slider for "New Version" to every day. Unless you are changing files very regularly there isn't a reason to have CrashPlan be more regular. You may also want to consider changing "Remove deleted files" to something other than "never" so if you ever lose a drive you don't inadvertantely restore every single file you ever deleted (or possibly just renamed).
6. In the "Advanced Settings" dialog:  
Data de-duplication: Minimal  
Compression: Off  
Encryption enabled: (up to you)  
Watch file system in real-time: unchecked  
Back up open files: unchecked  
<br>Given most media is already compressed and it's unlikely you'll have much duplicated data it's best to turn those off to keep from doing unnecessary work. CrashPlan does has some huristics to determine if something makes sense to compress a file but better safe than sorry.  
<br>Watching of the filesystem and backing up of open files are turned off since the former can greatly increase resource usage if you have many files and given the data is expected to be mostly "write once / read many" there is little reason to watch them for changes.
7. Save your changes

### Tips
#### Backing up sources when using drive pooling (mergerfs/mhddfs/etc.)
There are some limitations with how CrashPlan restores files. It doesn't allow a rsync style "restore only those files missing or changed" behavior. Therefore if using a pooling solution when attempting to restore the now missing files of a dead drive it is difficult to select only those missing files leading to you needing to restore files unnecessarily. To work around this create a new backup set and add the source mounts. It's probably best to set the backup set to the lowest priority so it doesn't interfere with the main backups. CrashPlan's dedup behavior will allow for these drives to be backed up rather quickly.

When a drive dies you can simply replace the drive and instruct CrashPlan to restore files from the dead source drives mount point to the new one. Specifics can be found in the [mergerfs & CrashPlan recovery guide](recovery_(mergerfs,crashplan).md).

#### Improving backup performance

Currently as the ammount of data backed up increases the slower the deduplication behavior by CrashPlan becomes and at the came time CPU utilization increases. While not ideal, until CrashPlan addresses this issue sufficiently, one can effectively disable data deduplication and speedup the backup procedure.

* Open `/usr/local/crashplan/conf/my.service.xml`
* Search for and change each `dataDeDupAutoMaxFileSizeForWan` value found to `1`. (`0` appears to mean `infinite`)
* Save the file.
* Restart the CrashPlan service. (Ubuntu: `sudo service crashplan restart`)

**NOTE:** Turning off de-duplication is not supported by Code42.
https://support.code42.com/CrashPlan/4/Configuring/Unsupported_Changes_To_CrashPlan_De-Duplication_Settings

#### Parallel backups & restores
CrashPlan only backs up one file at a time and will disable backups while restoring. Generally this is not a problem but if you have a very large set of files to back up and have the "Family" subscription you can install multiple instances of CrashPlan on the same machine but have CrashPlan think they are different machines.

Instead of one instance with N backup sets you can setup a few instances each with fewer backup sets. This can be used also to temporarily create a new instance so you can continue backing up with the original and restore data with the temp instance.

The easiest way to install multiple instances is to use Docker as described above. Simply create multiple instances with different names and different host ports. 

For example:

```
$ docker run -d --name=crashplan-music \
  --net=bridge \
  --restart=unless-stopped \
  -p 4280:4280 \
  -v /mnt:/mnt \
  -v /path/to/storage:/storage \
  gfjardim/crashplan
$ docker run -d --name=crashplan-movies \
  --net=bridge \
  --restart=unless-stopped \
  -p 5280:4280 \
  -v /mnt:/mnt \
  -v /path/to/storage:/storage \
  gfjardim/crashplan
$ docker run -d --name=crashplan-photos \
  --net=bridge \
  --restart=unless-stopped \
  -p 6280:4280 \
  -v /mnt:/mnt \
  -v /path/to/storage:/storage \
  gfjardim/crashplan
```

Connect to their GUIs usings ports 4280, 5280, and 6280 for music, movie, and photo instances respectively.

Remember that each instance takes up a not insignificant amount of memory and CPU so this setup should only be used if you have decent amount of RAM and CPU available or only temporarily when restoring.
