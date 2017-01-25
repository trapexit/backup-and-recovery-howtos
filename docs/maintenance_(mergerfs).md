# MergerFS Maintenance

### Change settings at runtime

MergerFS uses extended attributes (xattr) as an interface to manipulate settings at runtime. All non FUSE options (pooled drives, policies, minfreespace, moveonenospc, etc.) can be changed.

The values can be changed using generic xattr tools as described in the [mergerfs docs](https://github.com/trapexit/mergerfs#runtime) or using the tool [mergerfs.ctl](https://github.com/trapexit/mergerfs-tools).

### Audit / fix permissions

It's not uncommon for there to be duplicate files and directories across drives and there is nothing within mergerfs which attempts to keep permissions and ownership in sync as it would add a lot of overhead and could be a security risk.

To help with finding and correcting permissions the `mergerfs.fsck` tool is available.

https://github.com/trapexit/mergerfs-tools

### Deduplicate files / directories

It's common when drives fail to move files around and sometimes they aren't always cleaned up. The `mergerfs.dedup` tool will identify duplicates and offers ways to resolve which to keep.

https://github.com/trapexit/mergerfs-tools
