# /etc/fstab Setup Tips

### nobootwait
The option `nobootwait` can be very useful when dealing with possibly unstable mount points. Network mounts or in the case of many local drives where there is a possibility that one or more of them could fail to mount at startup. `nobootwait` will instruct the system to continue booting even if the drive is unavailable. If we are talking about drives storing media (rather than critical drives holding home directories or /var) then better to have the machine boot and some of your data missing than not booting at all.

Example:
```
# <file system> <mount point>   <type> <options>                             <dump>  <pass>
/dev/sdb        /mnt/data0      auto   defaults,nobootwait,errors=remount-ro 0       2
```

### EXT4
#### errors
The `errors` option for EXT{2,3,4} tells the system what to do when encountering an error.

There are three possible settings: `errors=remount-ro`, `errors=continue`, or `errors=panic`

* `errors=remount-ro` will place the drive into a read-only mode should it encounter an error in order to limit futher damage or corruption.
* `errors=continue` will "mark the filesystem as erroneous and continue." If in your experience most errors tend to be limited to specific blocks and you would prefer to manage the error condition manually then this option may be used.
* `errors=panic` will panic and halt the system. You probably don't want to use this on media drives.

### mergerfs

From: https://github.com/trapexit/mergerfs#options

```
# <file system>        <mount point>  <type>         <options>             <dump>  <pass>
/mnt/disk*:/mnt/cdrom  /storage       fuse.mergerfs  defaults,allow_other  0       0
```

For mounting via fstab to work you must have `mount.fuse` installed. For Ubuntu & Debian it is included in the `fuse` package.

The `*` (and other glob sigils) should not be escaped.
