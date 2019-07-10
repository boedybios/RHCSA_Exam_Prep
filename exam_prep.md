# RHCSA Exam Preparation

## Reset Root Password -> Super Important

-   Reboot the system
-   Enter Grub Edit menu by pressing `e`
-   Find `linux16` or `linuxefi` line
-   In the end of the line, add this: 

```bash
rd.break
```

-   Press `Ctrl+X` to start the boot process 
-   Remount the `sysroot` file system: 

```bash
# mount -o remount,rw /sysroot
```

-   Enter `sysroot` as `root`: 

```bash
# chroot /sysroot
```

-   Change root password: 

```bash
# passwd
```

-   Autorelabel

```bash
# touch /.autorelabel
```

-   Exit twice to reboot: 

```bash
# exit
# exit
```

## Configure Network and Hostname

-   For interactive way to configure both network and hostname is using `nmtui`

```bash
# nmtui
```

-   Restart network service

```bash
# systemctl restart network
```

-   Test the connection using `ping`

```bash
# ping example.com
```

## Configure Systemd Boot Target

-   Configure systemd boot target to `multi-user`

```bash
# systemctl set-default multi-user.target
```

-   Configure systemd boot target to `graphical`

```bash
# systemctl set-default graphical.target
```

-   Verify systemd boot target:

```bash
# systemctl get-default
```

## Configure SELinux for Enforcing or Permissive Mode

-   Set Enforcing Mode

```bash
# setenforce enforcing
```

-   Set Permissive Mode

```bash
# setenforce permissive
```

-   Verify Enforcing or Permissive Mode:

```bash
# getenforce
```

## Configure Yum Repository

-   Need a help? Find `/Example:`

```bash
# man yum.conf
```

-   Create and open a config file: 

```bash
# vim /etc/yum.repos.d/REPO_NAME.repo
```

```bash
[myrepoid]
name = Name of this repo
baseurl = http://server.example.com/repo
gpgcheck = 0
enabled = 1
```

-   Verify the repo configuration:

```bash
# yum clean all
# yum list all
# yum repolist
```

## Update Kernel

-   Add the appropriate kernel repository

```bash
# vim /etc/yum.repos.d/REPO_NAME.repo
```

```bash
[kernelrepo]
name = kernel repository
baseurl = http://server.example.com/kernel_repo
gpgcheck = 0
enabled = 1
```

```bash
# yum repolist
```

-   Check current kernel before update

```bash
# uname -rms
```

-   Update the kernel and reboot the system

```bash
# yum install kernel
# reboot
```

-   Verify the update kernel

```bash
# uname -rms
```

## Create Users and Groups

-   Create a group called `siswa`: 

```bash
# groupadd siswa
```

-   Verify the group (pay attention on its group id)

```bash
# tail /etc/group
```

-   Create users: `tejo`, `bejo`, `karti`:

```bash
# useradd tejo
# useradd bejo
# useradd karti
```

-   Create `wati` with userid 2992

```bash
# useradd -u 2992 wati 
```

-   Include user `tejo` and `bejo` to `siswa` as their secondary group:

```bash
# usermod -aG siswa tejo
# usermod -aG siswa bejo
```

-   Verify users (pay attention on username, uid, and group):

```bash
# id tejo
# id bejo
# id wati
```

-   Shit things happen in life, but don't worry; we can always remove a user from a group:

```bash
# gpasswd -d <user> <group>
```

-   Prevent user `karti` from login into shell:

```bash
# usermod -s /sbin/nologin karti
# su karti
```

-   Assign password to each user:

```bash
# passwd tejo
# passwd bejo
# passwd karti
```

## Create a Shared Directory

-   Create a directory

```bash
# mkdir -p /dir_bersama/kelompok_siswa
```

-   Configure group ownership of the shared directory to `siswa`

```bash
# chgrp siswa /dir_bersama/kelompok_siswa
```

-   The shared directory should be readable, writable, and accessible to the member of `siswa` only:

```bash
# chmod 770 /dir_bersama/kelompok_siswa
```

-   Verify the shared directory configuration:

```bash
# ls -ld /dir_bersama/kelompok_siswa
```

## Activate and Start a Service

-   Enable, start, and restart GUI service

```bash
# systemctl enable graphical.target
# systemctl start graphical.target
# systemctl restart graphical.target
```

## Configure NTP

-   Install chrony

```bash
# yum install chrony
```

-   Configure `chrony.conf` file:

```bash
# vim /etc/chrony.conf 
```

-   Add this entry to the file:

```bash
server time.example.com
```

-   Enable and start the service:

```bash
# systemctl enable chronyd
# systemctl start chronyd
```

-   Verify the NTP:

```bash
# chronyc sources -V
```

## Schedule a Job for a User

-   Register a job in crontab for user tejo

```bash
# crontab -eu tejo
```

```bash
# min  hour  date  month  day  command
  15    10    *      *     *   /bin/date >> /home/tejo/catatan_harian.txt
```

-   Enable and restart the service:

```bash
# systemctl enable crond
# systemctl start crond
```

-   Verify cron job for user tejo:

```bash
# crontab -el tejo
```

## Enable IP Forwarding

-   Configure `sysctl.conf`:

```bash
# vim /etc/sysctl.conf
```

-   Add this line to the file:

```bash
net.ipv4.ip_forward = 1
```

-   Reload the configuration file:

```bash
# sysctl -p
```

## Configure File Access Control List (FACL)

-   Backup `fstab` and use it as an example

```bash
# mkdir -p /root/backup/
# cp /etc/fstab /root/backup/fstab
# ls -l /root/backup/
```

-   User `tejo` can read and write the file:

```bash
# setfacl -m u:tejo:rw- /root/backup/fstab
```

-   User `bejo` cannot read or write the file:

```bash
# setfacl -m u:bejo:--- /root/backup/fstab
```

-   Other users can only read the file:

```bash
# chmod 744 /root/backup/fstab
```

-   Verify ACL of the file:

```bash
# getfacl /root/backup/fstab
```

## Create a Backup Archive File:

-   Create an archive file named `usr.local.tgz` in root home directory as a backup for `/usr/local/`

```bash
# tar -cvzf /root/usr.local.tgz /usr/local
```

## Create a New Physical Partition

-   List the partition table

```bash
# fdisk -l
```

-   Create a new partition of `/dev/sdb`(important menu `m`,`p`,`n`,`w`):

```bash
# fdisk /dev/sdb
```

-   Inform the OS of partition table changes

```bash
# partprobe
```

-   Verify the new partition:

```bash
# fdisk -l
```

-   Format the new partition using ext4:

```bash
# mkfs.ext4 /dev/sdb1
```

-   Create a directory for mounting point

```bash
# mkdir -p /my_mount
```

-   Get the UUID for the new partition

```bash
# blkid
```

-   Register the UUID into the fstab

```bash
# vim /etc/fstab
```

-   Add this line:

```bash
UUID=<uuid_for_sdb1> /my_mount ext4 defaults 0 0
```

-   Mount the new partition

```bash
# mount -a
```

-   Verify the mount point

```bash
# df -hT
```

## Create a New Swap Partition

-   Show the state of current swap partition (in mega bytes)

```bash
# free --mega
```

-   List the partition table

```bash
# fdisk -l
```

-   Create a new partition of `/dev/sdb`(important menu `m`,`p`,`n`,`l`,`t`,`w`) make to change the type to swap (`82`):

```bash
# fdisk /dev/sdb
```

-   Inform the OS of partition table changes

```bash
# partprobe
```

-   Verify the new partition:

```bash
# fdisk -l
```

-   Format the new partition as swap:

```bash
# mkswap /dev/sdb2
```

-   Activate the new swap partition:

```bash
# swapon /dev/sdb2
# swapon -a
```

-   Get the UUID for the new partition

```bash
# blkid
```

-   Register the UUID into the fstab

```bash
# vim /etc/fstab
```

-   Add this line:

```bash
UUID=<uuid_for_sdb2> swap swap defaults 0 0
```

-   Verify the new swap partition:

```bash
# swapon -s
# free --mega
```
