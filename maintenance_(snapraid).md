# SnapRaid Maintenance

Mostly from the SnapRaid [manual](http://www.snapraid.it/manual).

### Syncing

With SnapRaid the data on the drives need to have parity information calculated and stored on a regular basis.

```
# snapraid sync
```

It's probably best to do this nightly (or weekly if your data doesn't change much). 

[zackreed.me](http://zackreed.me/articles/83-updated-snapraid-sync-script) has a tutorial and custom script which will largely automate running of sync and verifying data.

### Scrubbing

Scrubbing the pool periodically will look for data and/or parity errors. It verifies the data in the pool with the hash which was computed via the `sync` command.

```
# snapraid scrub
```

As it scrubs any errors will be listable via the `status` command.

```
# snapraid status
```

To have snapraid try to fix the errors:

```
# snapraid -e fix
```

Confirming the fix succeeded:

```
# snapraid -p bad scrub
```



