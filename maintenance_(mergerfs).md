# MergerFS Maintenance

### Change settings at runtime

MergerFS uses extended attributes (xattr) as an interface to manipulate settings at runtime. All non FUSE options (pooled drives, policies, minfreespace, moveonenospc, etc.) are supported.

https://github.com/trapexit/mergerfs#runtime

### Audit / fix permissions

It's not uncommon for there to be duplicate files and directories across drives and there is nothing within mergerfs which attempts to keep permissions and ownership in sync as it'd add a lot of overhead and could be a security risk.

To help with this the fsck.mergerfs tool was created.

https://github.com/trapexit/mergerfs-tools

### Deduplicate files / directories

It's common when drives fail to move files around and sometimes they aren't always cleaned up. The mergerfs.dedup tool will identify duplicates and offers ways to resolve which to keep.

https://github.com/trapexit/mergerfs-tools
