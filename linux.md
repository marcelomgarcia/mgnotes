Linux Administration
===================

# Firewall 

Configuring the `ufw` firewall [to allow DNS](https://www.cyberciti.biz/faq/howto-open-dns-port-53-using-ufw-ubuntu-debian/) traffic:

```
sudo ufw allow dns
```

# Cleaning Apt Cache

Cleaning apt cache is important to [free up space and remove old package files](https://itsfoss.com/clear-apt-cache/) that are no longer needed. 

Inspecting the `/var/cache/apt/archives` directory can help you understand how much space is being used by cached packages:


```bash
root@lthlibprod:/var/cache/apt/archives# ls -1 | wc -l
178
root@lthlibprod:/var/cache/apt/archives# du -sh .
901M    .
root@lthlibprod:/var/cache/apt/archives#
```

Cleaning the apt cache can be done using the following command:

```bash
root@lthlibprod:/var/cache/apt/archives# apt clean
root@lthlibprod:/var/cache/apt/archives#
root@lthlibprod:/var/cache/apt/archives # Checking the size of the directory again
root@lthlibprod:/var/cache/apt/archives# du -sh .
32K     .
root@lthlibprod:/var/cache/apt/archives# 
```

# Finding Files

Excluding a directory from the search of the `find` command:

```bash
root@lthlibprod:/home/sftpsap/patron_info# find . -path "./.snapshot" -prune -o -type f -ctime +60
./Organizations20241230013006.TXT
./Organizations20241118081310.TXT
(...)
```


# Formating USB

Formating USB using `parted` to use [100% of the free space](https://ngelinux.com/how-to-create-full-size-one-partition-using-parted-command-in-linux/).

Locate the USB drive with `dmesg`

```
[root@valinor ~]# dmesg -T | tail
[Fri Sep  5 13:31:35 2025] usb 4-2: SerialNumber: 040137b5db4cd1c20aa2de9856e82256eb1218f41ca724c7876ea24cc6797621b3880000000000000000000040547ab1ff936c18a955810793ac55a1
[Fri Sep  5 13:31:35 2025] usb-storage 4-2:1.0: USB Mass Storage device detected
[Fri Sep  5 13:31:35 2025] scsi host2: usb-storage 4-2:1.0
[Fri Sep  5 13:31:36 2025] scsi 2:0:0:0: Direct-Access      USB      SanDisk 3.2Gen1 1.00 PQ: 0 ANSI: 6
[Fri Sep  5 13:31:36 2025] sd 2:0:0:0: [sda] 60088320 512-byte logical blocks: (30.8 GB/28.7 GiB)
[Fri Sep  5 13:31:36 2025] sd 2:0:0:0: [sda] Write Protect is off
[Fri Sep  5 13:31:36 2025] sd 2:0:0:0: [sda] Mode Sense: 43 00 00 00
[Fri Sep  5 13:31:36 2025] sd 2:0:0:0: [sda] Write cache: disabled, read cache: enabled, doesn't support DPO or FUA
[Fri Sep  5 13:31:36 2025]  sda: sda1 sda2  <-------- !!!!
[Fri Sep  5 13:31:36 2025] sd 2:0:0:0: [sda] Attached SCSI removable disk
[root@valinor ~]#
[root@valinor ~]#
```


Select the USB to use with `parted`:

```
[root@valinor ~]# parted /dev/sda
GNU Parted 3.6
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print
Model:  USB  SanDisk 3.2Gen1 (scsi)
Disk /dev/sda: 30.8GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size   Type     File system  Flags
 2      1260MB  1520MB  260MB  primary  fat32        esp

(parted) rm 2
(parted) print
Model:  USB  SanDisk 3.2Gen1 (scsi)
Disk /dev/sda: 30.8GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start  End  Size  Type  File system  Flags

(parted) mklabel msdos
Warning: The existing disk label on /dev/sda will be destroyed and all data on this disk will be lost. Do you want to continue?
Yes/No? yes
(parted) print
Model:  USB  SanDisk 3.2Gen1 (scsi)
Disk /dev/sda: 30.8GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start  End  Size  Type  File system  Flags

(parted) mkpart primary fat32 0% 100%
(parted) print
Model:  USB  SanDisk 3.2Gen1 (scsi)
Disk /dev/sda: 30.8GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  30.8GB  30.8GB  primary  fat32        lba

(parted) quit
Information: You may need to update /etc/fstab.

[root@valinor ~]# exit
logout
```


Then format the USB on Windows.
