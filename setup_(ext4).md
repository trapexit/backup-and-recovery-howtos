# EXT4 Setup

### Format

```
# mkfs.ext4 -m 0 -T largefile4 -L <label> -l /root/<device>.badblocks /dev/<device>
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
16 inodes, 4096 blocks
0 blocks (0.00%) reserved for the super user
First data block=0
1 block group
32768 blocks per group, 32768 fragments per group
16 inodes per group

Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done
```

* -m <reserved-blocks-percentage>: Reserved blocks for super-user. We set it to zero because these drives aren't used in a way where that really matters.a
* -T <usage-type>: Specifies how the filesystem is going to be used so optimal paramters can be chosen. Types are defined in `/etc/mke2fs.conf`. We set it to `largefile4` because we expect fewer, large files relative to typical usage. If you expect a large number of files or are unsure simply remove the option all together.
* -L <label>: Sets the label for the filesystem. A suggested format is: SIZE-MANUFACTURE-N. For example: `2.0TB-Seagate-0` for the first 2.0TB Seagate drive installed.
* -l <filename>: Read bad blocks list from `filename`. Only use this if you first ran the [burnin tests](setup_(storage_device).md).

It's generally a good idea to format the raw device rather than creating partitions.

1. The partition is mostly useless to us since we plan on using the entire drive for storage.
2. We won't need to worry about MBR vs GPT.
3. We won't need to worry about block alignment which can effect performance if misaligned.
4. When a 512e/4k drive is moved between a native SATA controller and a USB SATA adaptor there won't be partition block misalignment. Often USB adapters will report 4k/4k to the OS while the drive will report 512/4k causing the OS to fail to find the paritions or filesystems. This can be fixed but no tools exist to do the procedure automatically.
