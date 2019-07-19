# RHCSA (RHEL 8) Exam Preparation

## Reset Root Password -> Super Important

-   Reboot the system
-   Enter Grub Edit menu by pressing `e`
-   Find `linux16` or `linuxefi` line
-   In the end of the line, add this: 

```bash
rd.break
```

-   Press `Ctrl+X` to start the boot process 
-   Remount the `sysroot` file system with read and write permission: 

```bash
# mount -o remount,rw /sysroot
```

-   Configure `/sysroot` as `/`: 

```bash
# chroot /sysroot
```

-   Change root password: 

```bash
# passwd
```

-   Ask `SELinux` to relabel the entire `SELinux` label:

```bash
# touch /.autorelabel
```

-   Exit twice to reboot: 

```bash
# exit
# exit
```

## Configure Network and Hostname

-   For interactive way to configure both `network` and `hostname`, use `nmtui`

```bash
# nmtui
```

-   Restart `NetworkManager` service

```bash
# systemctl restart NetworkManager
```

-   Verify the network configuration:

```bash
# nmcli
```

-   Test the connection using `ping`

```bash
# ping example.com
```

## Configure Yum Repository and Software Installation

-   Create and open a new configuration file: 

```bash
# vim /etc/yum.repos.d/REPO_NAME.repo
```

```bash
[myrepoid]
name = Name of this repo
baseurl = http://server.example.com/repo
enabled = 1
gpgcheck = 0
```

-   Alternatively we can use `yum-config-manager`:

```bash
# yum-config-manager --add-repo "http://server.example.com.repo"
```

-   Verify the repository configuration:

```bash
# yum repolist
```

-   Install `cups` from a repository:

```bash
# yum install cups
```

-   Install `httpd 2.4` from a repository with a `common` stream module:

```bash
# yum module list
# yum module install httpd:2.4/common
```

-   Install a package from rpm file:

```bash
# yum localinstall file.rpm
```

-   Remove/uninstall `cups` from the system:

```bash
# yum remove cups
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
enabled = 1
gpgcheck = 0
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
# yum update kernel
# reboot
```

-   Verify the update kernel

```bash
# uname -rms
```

## Configure Default Systemd Boot Target

-   Configure default `systemd` boot target to `multi-user`

```bash
# systemctl set-default multi-user.target
```

-   Configure default `systemd` boot target to `graphical`

```bash
# systemctl set-default graphical.target
```

-   Verify `systemd` boot target:

```bash
# systemctl get-default
```

## Configure SELinux for Enforcing or Permissive Mode

-   Set `SELinux` mode to Enforcing [warning: this one is not permanent]&#x3A;

```bash
# setenforce enforcing
```

-   Set `SELinux` mode to Permissive [warning: this one is not permanent]&#x3A;

```bash
# setenforce permissive
```

-   To make it permanent, make sure to configure the `SELinux` `config` file

```bash
# vim /etc/selinux/config 
```

```bash
SELINUX = enforcing
```

-   Verify the `SELinux` Mode:

```bash
# getenforce
# sestatus
```

## SELinux Troubleshoot

-   Find `sealert` log message:

```bash
# cat /var/log/message | grep sealert
```

-   Learn more from the `sealert` details:

```bash
# sealert -l <sealert_id>
```

-   Learn from audit message:

```bash
# ausearch -m AVC -ts recent
```

-   Adjust`SELinux` context using `semanage`:

```bash
# semanage fcontex -a -t <target_context> <adjustment>
```

-   Apply `SELinux` context:

```bash
# restorecon -R <target_files>
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

-   Create `wati` with `userid` 2992

```bash
# useradd -u 2992 wati 
```

-   Include user `tejo` and `bejo` to `siswa` as their secondary group:

```bash
# usermod -aG siswa tejo
# usermod -aG siswa bejo
```

-   Verify users (pay attention on `username`, `uid`, and `group`):

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

-   The shared directory should be readable, writable, and accessible to the member of `siswa` only [make it applied for the future as well, therefore we need g+s]&#x3A;

```bash
# chmod 2770 /dir_bersama/kelompok_siswa
```

-   Verify the shared directory configuration:

```bash
# ls -ld /dir_bersama/kelompok_siswa
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

-   Group `badguys` cannot read or write the file:

```bash
# setfacl -m g:badguys:--- /root/backup/fstab
```

-   By default, group `goodguys` can access directory `/root/backup/` and read the files within the directory:

```bash
# setfacl -Rm d:g:goodguys:r-X /root/backup/
```

-   Verify `ACL` of the file:

```bash
# getfacl /root/backup/fstab
```

## Configure SSH Key

-   Generate `ssh key`:

```bash
# ssh-keygen
```

-   Copy `ssh key` to target server:

```bash
# ssh-copy-id user@myserver
```

-   Verify by login to the target server:

```bash
# ssh myserver
```

## Activate and Start a Service

-   Enable and start `httpd` service

```bash
# systemctl enable --now httpd
```

## Configure NTP (Network Time Protocol)

-   Install `chrony` if not installed: 

```bash
# yum install chrony
```

-   Configure `chrony.conf` file:

```bash
# vim /etc/chrony.conf 
```

-   Add this entry to the file:

```bash
server time.example.com iburst
```

-   Enable and start the service:

```bash
# systemctl enable --now chronyd
```

-   Turn on the time syncronisation:

```bash
# timedatectl set-ntp yes
```

-   Verify the `NTP`:

```bash
# chronyc sources -v
# timedatectl
```

## Schedule a Job for a User

-   Register a job in `crontab` for user `tejo`

```bash
# crontab -eu tejo
```

```bash
# min  hour  date  month  day  command
  15    10    *      *     *   /bin/date >> /home/tejo/catatan_harian.txt
```

-   Enable and start the service:

```bash
# systemctl enable --now crond
```

-   Verify `cron job` for user `tejo`:

```bash
# crontab -el tejo
```

## Create a Backup Archive File:

-   Create a backup directory `/tmp/my_backup`:

```bash
# mkdir /tmp/my_backup
```

-   Copy all the files within the system which are owned by user `tejo` to the backup directory:

```bash
# find / -user tejo -exec cp -rfp {} /tmp/my_backup \;
```

-   Create an archive file named `tejo.backup.tar.gz` in root home directory as a backup for the backup directory

```bash
# tar -cvzf /root/tejo.backup.tar.gz /tmp/my_backup
```

-   Verify:

```bash
# ls -l /root
```

## Extract a Line from a File with String Matching

-   Extract a live in `/etc/passwd` contains `karti` and stores it into a file `/root/user.karti.txt`

```bash
# cat /etc/passwd | grep karti > /root/user.karti.txt
```

-   Verify:

```bash
# ls -l /root
# cat /root/user.karti.txt
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

## Start Cockpit Service

-   Enable and start a cockpit service

```bash
# systemctl enable --now cockpit.service
```

-   Open web browser and access the `http://servername:9090`

## Create a New Physical Partition

-   List the partition table on `/dev/sdb`:

```bash
# lsblk -fp
# parted /dev/sdb print
```

-   Create partition label (`msdos` or `gpt`) on `/dev/sdb` [use `m` for help in `fdisk` menu] + [always verify using `p` before writing it using `w`]

```bash
# fdsik /dev/sdb
```

-   Create a new partition on `/dev/sdb` [use `m` for help in `fdisk` menu] + [always verify using `p` before writing it using `w`]

```bash
# fdisk /dev/sdb
```

-   Verify the new partition:

```bash
# lsblk -fp
# parted /dev/sdb print
```

-   Register the new partition:

```bash
# udevadm settle
```

-   Format the new partition using `ext4`:

```bash
# mkfs.ext4 -L volume_label /dev/sdb1
```

-   Create a directory for mounting point

```bash
# mkdir -p /my_mount
```

-   Get the `UUID` for the new partition

```bash
# lsblk -fp
# parted /dev/sdb print
```

-   Register the `UUID` into the `fstab`

```bash
# vim /etc/fstab
```

-   Add this line:

```bash
UUID=<uuid_for_sdb1> /my_mount ext4 defaults 0 0
```

-   Or alternatively we can use device name

```bash
/dev/sdb1 /my_mount ext4 defaults 0 0
```

-   Update `systemd` for the system to register the new `/etc/fstab` configuration:

```bash
# systemctl daemon-reload
# mount -a
```

-   Verify the mount point

```bash
# lsblk -fp
```

## Create a New Swap Partition

-   Show the state of current swap partition (in mega bytes)

```bash
# free -m
```

-   List the partition table on `/dev/sdb`:

```bash
# lsblk -fp
# parted /dev/sdb print
```

-   Create a new partition on `/dev/sdb` [use `m` for help in `fdisk` menu, don't forget to adjust the partition type to `linux-swap` using `t`] + [always verify using `p` before writing it using `w`]

```bash
# fdisk /dev/sdb
```

-   Register the new partition:

```bash
# udevadm settle
```

-   Verify the new partition:

```bash
# lsblk -fp
# parted /dev/sdb print
```

-   Format the new partition as swap:

```bash
# mkswap -L swap_label /dev/sdb2
```

-   Activate the new swap partition:

```bash
# swapon /dev/sdb2
# swapon -a
```

-   Get the `UUID` for the new partition

```bash
# lsblk -fp
# parted /dev/sdb print
```

-   Register the `UUID` into the `fstab`

```bash
# vim /etc/fstab
```

-   Add this line:

```bash
UUID=<uuid_for_sdb2> swap swap defaults 0 0
```

-   Or alternatively we can use device name

```bash
/dev/sdb2 swap swap defaults 0 0
```

-   Verify the new swap partition:

```bash
# swapon -s
# free -m
```

## Creating Logical Volumes

-   Create two physical partitions on `/dev/sdc` with the size of `500MB` each. We can do it using `fdisk` but make sure to adjust the type to `Linux LVM` using `t` [always verify using `p` before writing it using `w`]&#x3A;

```bash
# fdisk /dev/sdc
```

-   Verify the partitions:

```bash
# lsblk -fp
# parted /dev/sdc print
```

-   Register the new partition:

```bash
# udevadm settle
```

-   Include the two newly created partitions (`/dev/sdc1` and `/dev/sdc2`) as `PV` (Physical Volume):

```bash
# pvcreate /dev/sdc1 /dev/sdc2
```

-   Verify the `PV`:

```bash
# pvdisplay
```

-   Create a new VG (Volume Group) built from the two `PV` (`/dev/sdc1` and `/dev/sdc2`) and named it `vg01`:

```bash
# vgcreate vg01 /dev/sdc1 /dev/sdc2
```

-   Verify the `VG`:

```bash
# vgdisplay
```

-   Create a new `LV` (Logical Volume) from `/dev/vg01` with the size of `600MB` and named it `lv01`:

```bash
# lvcreate -n lv01 -L 600M vg01
```

-   Verify the `LV`:

```bash
# lvdisplay
```

-   Format the newly created `LV` (`/dev/vg01/lv01`) with `XFS` as its file system:

```bash
# mkfs.xfs -L volume_label /dev/vg01/lv01
```

-   Create a mount point on `/logical_storage`:

```bash
# mkdir /logical_storage
```

-   Edit the `/etc/fstab` and add this line:

```bash
/dev/vg01/lv01  /logical_storage  xfs  defaults  0  0
```

-   Update `systemd` for the system to register the new `/etc/fstab` configuration:

```bash
# systemctl daemon-reload
# mount -a
```

-   Verify the mount point

```bash
# lsblk -fp
# df -h /logical_storage
```

## Extending Logical Volumes

-   Create a new physical partition from `/dev/sdc` with the size of `500MB`. We can do it using `fdisk` but make sure to adjust the type to `Linux LVM` using `t` [always verify using `p` before writing it using `w`]&#x3A;

```bash
# fdisk /dev/sdc
```

-   Verify the partitions:

```bash
# lsblk -fp
# parted /dev/sdc print
```

-   Register the new partition:

```bash
# udevadm settle
```

-   Include the newly created partitions (`/dev/sdc3`)  as `PV` (Physical Volume):

```bash
# pvcreate /dev/sdc3
```

-   Verify the `PV`:

```bash
# pvdisplay
```

-   Extend the VG (`/dev/vg01`) using the new `PV` (`/dev/sdc3`):

```bash
# vgextend /dev/vg01 /dev/sdc3
```

-   Verify the VG:

```bash
# vgdisplay
```

-   Extend the `LV` (`/dev/vg01/lv01`) to `1.1G`:

```bash
# lvextend -L 1.1G /dev/vg01/lv01
```

-   Verify the `LV`:

```bash
# lvdisplay
```

-   Extend the `XFS` file system:

```bash
# xfs_growfs /logical_storage
```

-   Verify the usable mount space:

```bash
# df -h /logical_storage
```

## Configure Autofs

-   Install `autofs` if it isn't installed:

```bash
# yum install autofs
```

-   Configure `autofs` to disable `nfs` with `udp` and `version 3`:

```bash
# nfsconf --set nfsd udp n
# nfsconf --set nfsd vers3 n
```

-   Configure `autofs` to enable `nfs` version `4`:

```bash
# nfsconf --set nfsd vers4 y
```

-   Create mount point named `remote_storage`:

```bash
# mkdir /remote_storage
```

-   Create `automounter` file with name `mapper` to access `/shares` from `myserver`:

```bash
# vim /etc/auto.master.d/mapper.autofs
```

-   Put this line:

```bash
/remote_storage  /etc/auto.mapper
```

-   Create map file as defined in the `automounter` file:

```bash
# vim /etc/auto.mapper
```

-   Put this line:

```bash
*  -rw,sync,fstype=nfs4  my_server:/shares/&
```

-   Enable and start `autofs` service:

```bash
# systemctl enable --now autofs
```

-   Verify:

```bash
# ls -l /remote_storage
```
