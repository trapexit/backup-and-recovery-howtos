# BTRFS Maintenance

### Online Checking (Scrub)

[scrub](https://btrfs.wiki.kernel.org/index.php/Glossary) is "an online filesystem checking tool. Reads all the data and metadata on the filesystem, and uses checksums and the duplicate copies from RAID storage to identify and repair any corrupt data."

It's a good idea to run a scrub occasion (every month?) to catch and possibly repair any corrupted data.

```
# btrfs scrub start <mountpoint>
# btrfs scrub status <mountpoint>
```

### Rebalancing Data

[balance](https://btrfs.wiki.kernel.org/index.php/Glossary) "passes all data in the filesystem through the allocator again. It is primarily intended to rebalance the data in the filesystem across the devices when a device is added or removed. A balance will regenerate missing copies for the redundant RAID levels, if a device has failed."

```
# btrfs balance start <mountpoint>
# btrfs balance status <mountpoint>
```
