Linux Administration
===================

# Tools

| Tool | Sample |
| ---- | ------ |
| [zoxide](https://github.com/ajeetdsouza/zoxide) (`z`) | A smart `cd` command |
| [fzf](https://github.com/junegunn/fzf) | A fuzzy finder. A much better and faster `find` command |
| [ripgrep](https://github.com/BurntSushi/ripgrep) | A much better version of `grep` command |

# Firewall 

Configuring the `ufw` firewall [to allow DNS](https://www.cyberciti.biz/faq/howto-open-dns-port-53-using-ufw-ubuntu-debian/) traffic:

```
garcm0b@KW20207:~$ sudo ufw allow 53/tcp comment 'Open port DNS tcp port 53'
Rule added
garcm0b@KW20207:~$ sudo ufw allow 53/udp comment 'Open port DNS udp port 53'
Rule added
garcm0b@KW20207:~$
garcm0b@KW20207:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), deny (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
11434                      ALLOW IN    Anywhere
22/tcp (OpenSSH)           ALLOW IN    Anywhere
53/tcp                     ALLOW IN    Anywhere                   # Open port DNS tcp port 53
53/udp                     ALLOW IN    Anywhere                   # Open port DNS udp port 53
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

# LDAP

Searching the LDAP

```
# Initialize Kerberos cache
joe@lthlibmig:~$ kinit -a joe
Password for joed@DOMAIN.EDU:
# Search LDAP
joe@lthlibmig:~$ ldapsearch -H ldap://my.ldap.server -LLL -b "DC=KK,DC=EDU,DC=SS" cn=joe
SASL/GSS-SPNEGO authentication started
(...)
```

# Umask

[Setting the `umask`](https://www.cyberciti.biz/tips/understanding-linux-unix-umask-value-usage.html) for **all** users on the system (Ubuntu).

```
mgarcia@valinor:~$ cat /etc/profile.d/set-umask-for-all-users.sh
umask 022
mgarcia@valinor:~$
```

# Rsync

Synchronizing directories skipping the `.git` directory. The following command will only show the files that would have been synchronized (_dry-run_)

```
  rsync -avin /home/garcm0b/Work/irts/ .
```

Options breakdown:

  - `-a`: archive mode (recursive, preserves permissions, timestamps, etc.)
  - `-v`: verbose (shows file names)
  - `-i`: itemize changes (shows detailed info about what would change)
  - `-n`: dry-run (don't actually copy anything)

The -i flag will show output like:

  - `>f+++++++++` - new file that would be copied
  - `>f.st......` - file exists but is newer/different in source
  - `.f` - file is identical (won't be copied)

If you want a simpler output showing only files that would be transferred, you can omit the -i:

```
  rsync -avn /home/garcm0b/Work/irts/ .
```

> Note: The trailing slash on the source path (/home/garcm0b/Work/irts/) means "copy the contents of this directory" rather than "copy the directory
  itself".


To skip the `.git` directory, add the `--exclude` option:

```
  rsync -avin --exclude='.git' /home/garcm0b/Work/irts/ .
```

Or with simpler output (without itemized changes):

```
  rsync -avn --exclude='.git' /home/garcm0b/Work/irts/ .
```

You can also exclude multiple items if needed:

```
  rsync -avin --exclude='.git' --exclude='__pycache__' --exclude='*.pyc' --exclude='venv' /home/garcm0b/Work/irts/ .
```

  The `--exclude='.git'` will skip the entire `.git` directory and all its contents.

# Finding Files

## Find Command

Excluding a directory from the search of the `find` command:

```bash
root@lthlibprod:/home/sftpsap/patron_info# find . -path "./.snapshot" -prune -o -type f -ctime +60
./Organizations20241230013006.TXT
./Organizations20241118081310.TXT
(...)
```

## FD Find

Using a new version [fd find](https://github.com/sharkdp/fd)

```
garcm0b@KW20207:~$ fdfind --help
A program to find entries in your filesystem

Usage: fdfind [OPTIONS] [pattern] [path]...
(...)
```

# OpenSSL

Generate random passwords with OpenSSL. Use `hex` for a hexadecimal or `base64` for alphanumeric pseudo-random passwords

```
mgarcia@valinor:~$ openssl rand -base64 16
dAq2FeDkBNsO+O/AMgA3+A==
mgarcia@valinor:~$ openssl rand -hex 16
a35abff0cfab0281a191658b0d046293
mgarcia@valinor:~$ 
```

# SSH Tunnel

Accessing the MySQL database via SSH tunnel when the port `3306` is blocked by the firewall.

```
mgarcia@PC-KL-26743:~$ ssh -L 3336:localhost:3306 db
mgarcia@PC-KL-26743:~$ mysql -h localhost -P 3336 -u irts -p test_irts
Enter password:
Reading table information for completion of table and column names
(...)

MySQL [test_irts]>
```

# Tar

Extracting a `.tar.gz` file to a specific directory:

```bash
tar -xzf archive.tar.gz -C /path/to/destination
```

Extracting a single `.gz` file to a specific directory:

```bash
gunzip -c file.gz > /path/to/destination/file
```

Removing a file from inside a `tar` file

```
a-garcm0b@lthlibprod:/tmp$ tar tvf cert_kaust.tar
drwxr-xr-x root/root         0 2024-04-21 14:18 certs/
-rw-r--r-- root/root      4240 2024-04-15 15:37 certs/ssl.pem_before_2024-04-21
-r-------- root/root      1675 2022-06-21 15:54 certs/ssl.key_before_2024-04-21
-r-------- root/root      1675 2023-05-31 15:21 certs/ssl.key
-rw-r--r-- root/root      4240 2024-04-15 15:41 certs/ssl.pem
a-garcm0b@lthlibprod:/tmp$
a-garcm0b@lthlibprod:/tmp$ sudo tar --delete --file cert_kaust.tar certs/ssl.pem_before_2024-04-21
a-garcm0b@lthlibprod:/tmp$ sudo tar --delete --file cert_kaust.tar certs/ssl.key_before_2024-04-21
a-garcm0b@lthlibprod:/tmp$ tar tvf cert_kaust.tar
drwxr-xr-x root/root         0 2024-04-21 14:18 certs/
-r-------- root/root      1675 2023-05-31 15:21 certs/ssl.key
-rw-r--r-- root/root      4240 2024-04-15 15:41 certs/ssl.pem
a-garcm0b@lthlibprod:/tmp$
```

# Canonical Multipass

## Troubleshooting

Troubleshooting Multipass [authentication error](https://documentation.ubuntu.com/multipass/latest/how-to-guides/customise-multipass/authenticate-clients-with-the-multipass-service/):

```
garcm0b@KW20207:~/Work/IRTSv2/updates$ multipass list
list failed: The client is not authenticated with the Multipass service.
Please use 'multipass authenticate' before proceeding.
garcm0b@KW20207:~/Work/IRTSv2/updates$ 
garcm0b@KW20207:~/Work/IRTSv2/updates$ sudo cat ~/snap/multipass/current/data/multipass-client-certificate/multipass_cert.pem | sudo tee -a /var/snap/multipass/common/data/multipassd/authenticated-certs/multipass_client_certs.pem > /dev/null
garcm0b@KW20207:~/Work/IRTSv2/updates$
garcm0b@KW20207:~/Work/IRTSv2/updates$ sudo snap restart multipass
2025-11-11T05:45:53Z INFO Waiting for "snap.multipass.multipassd.service" to stop.
Restarted.
garcm0b@KW20207:~/Work/IRTSv2/updates$
garcm0b@KW20207:~/Work/IRTSv2/updates$ multipass list
Name                    State             IPv4             Image
btrix                   Stopped           --               Ubuntu 24.04 LTS
garcm0b@KW20207:~/Work/IRTSv2/updates$
```

# Formatting USB

Formatting USB using `parted` to use [100% of the free space](https://ngelinux.com/how-to-create-full-size-one-partition-using-parted-command-in-linux/).

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

# Zombie Processes

Zombie processes (defunct processes) are processes that have completed execution but still have an entry in the process table.

## Finding Zombie Processes

Using `ps`:

```bash
ps aux | awk '$8 ~ /Z/'
```

The `STAT` column (the 8th column) will show `Z` for zombie processes.

Alternatively, to just get a count:

```bash
ps aux | awk '$8 ~ /Z/' | wc -l
```

Using `top`:

The summary line at the top of the `top` command displays the number of zombie processes:

```text
Tasks: 235 total,   1 running, 234 sleeping,   0 stopped,   0 zombie
```
