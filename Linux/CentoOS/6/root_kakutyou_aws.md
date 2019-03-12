# rootが大きくならない・・・
install cloud-utils-growpart

```
# wget http://ftp.riken.jp/Linux/fedora/epel/6/x86_64/Packages/d/dracut-modules-growroot-0.20-2.el6.noarch.rpm
# wget http://ftp.riken.jp/Linux/fedora/epel/6/x86_64/Packages/c/cloud-utils-growpart-0.27-10.el6.x86_64.rpm
# rpm -ihv cloud-utils-growpart-0.27-10.el6.x86_64.rpm dracut-modules-growroot-0.20-2.el6.noarch.rpm
# LC_ALL="en_US.UTF-8" growpart /dev/xvda 1
# reboot
# resize2fs /dev/xvda1
```
で増える


ce5系などは
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
(parted) print
Model: Xen Virtual Block Device (xvd)
Disk /dev/xvdf: 62914560s
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start  End        Size       Type     File system  Flags
 1      63s    20964824s  20964762s  primary  ext3         boot

(parted) rm 1
(parted) mkpart
Partition type?  primary/extended? primary
File system type?  [ext2]? ext3
Start? 63s
End? 62914559s
Warning: The resulting partition is not properly aligned for best performance.
Ignore/Cancel? Ignore
(parted) print
Model: Xen Virtual Block Device (xvd)
Disk /dev/xvdf: 62914560s
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start  End        Size       Type     File system  Flags
 1      63s    62914559s  62914497s  primary  ext3

(parted) set 1 boot on
(parted) print
Model: Xen Virtual Block Device (xvd)
Disk /dev/xvdf: 62914560s
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start  End        Size       Type     File system  Flags
 1      63s    62914559s  62914497s  primary  ext3         boot

(parted) quit
```
