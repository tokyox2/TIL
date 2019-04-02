# rootが大きくならない・・・
## CentOS6なんかだと
```
# yum --disablerepo=*   --enablerepo=idc* -c ./mwex-54.repo install cloud-utils-growpart
# LC_ALL="en_US.UTF-8" growpart /dev/xvda 1
# reboot
# resize2fs /dev/xvda1
で増やせた！
```

## CentOS5なんかだと
```
# parted /dev/xvdf
GNU Parted 2.1
Using /dev/xvdf
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print
Model: Xen Virtual Block Device (xvd)
Disk /dev/xvdf: 32.2GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start   End     Size    Type     File system  Flags
 1      32.3kB  10.7GB  10.7GB  primary  ext3         boot

(parted) unit s
(parted) set 1 boot on
(parted) print
Model: Xen Virtual Block Device (xvd)
Disk /dev/xvdf: 62914560s
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start  End        Size       Type     File system  Flags
 1      63s    20964824s  20964762s  primary  ext3         boot
```
