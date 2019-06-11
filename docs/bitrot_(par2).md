# Managing BitRot with Parchive (Par2)

[Parchive](https://en.wikipedia.org/wiki/Parchive) is an erasure code system which produces *par* files for data integrity verification with the ability to recover / repair corrupted and missing data.

### Install par2cmdline

#### Debian

```
$ sudo apt install par2
```

#### Generic

```
$ git clone https://github.com/Parchive/par2cmdline
$ cd par2cmdline
$ ./automake.sh
$ ./configure
$ make
$ make check
$ make install
```

### Creating par2 files

* -n1 : Only create 1 par2 file (besides index).
* -r5 : Create redundancy of 5%. Set higher if file importance is higher.

```
$ par2 c -n1 -r5 <file>
```

```
$ find <path> -type f -not -name "*.par2" -exec par2 c -n1 -r5 {} \;
```

### Verify file

```
$ par2 v <file>.par2
```

```
$ find <path> -type f -name "*.par2" -exec par2 v {} \;
```

### Repair file

```
$ par2 r <file>.par2
```

### Creating a `par2` directory

#### Create the par2 files

```
$ find <path> -type f -not -name "*.par2" -exec par2 c -n1 -r5 {} \;
```

#### Move to `par2` directory

```
$ rsync -avR --remove-source-files --include="*.par2" --exclude="par2/" --include="*/" --exclude="*" <basepath>/ <basepath>/par2/
```

#### Verify from `par2` directory

Run in base directory with *par2* directory.

```
#!/bin/bash

IFS=$'\n'
for x in $(find "par2" -type f -name "*.par2")
do
    par2 v -q -B "$(dirname "${x#par2/}")" "${x}"
done
```
