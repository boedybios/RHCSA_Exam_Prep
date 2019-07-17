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

-   Ask SELinux to relabel the entire SELinux label:

```bash
# touch /.autorelabel
```

-   Exit twice to reboot: 

```bash
# exit
# exit
```

## Configure Network and Hostname

-   For interactive way to configure both network and hostname, use `nmtui`

```bash
# nmtui
```

-   Restart NetworkManager service

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

## Configure Default Systemd Boot Target

-   Configure default systemd boot target to `multi-user`

```bash
# systemctl set-default multi-user.target
```

-   Configure default systemd boot target to `graphical`

```bash
# systemctl set-default graphical.target
```

-   Verify systemd boot target:

```bash
# systemctl get-default
```

## Configure SELinux for Enforcing or Permissive Mode

-   Set SELinux mode to Enforcing:

```bash
# setenforce enforcing
```

-   Set SELinux mode to Permissive:

```bash
# setenforce permissive
```

-   To make it permanent, make sure to configure the SELinux config file

```bash
# vim /etc/selinux/config 
```

```bash
SELINUX = enforcing
```

-   Verify the SELinux Mode:

```bash
# getenforce
```

## Configure Yum Repository and Software Installation

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

-   Alternatively we can use `yum-config-manager`:

```bash
# yum-config-manager --add-repo "http://server.example.com.repo"
```

-   Verify the repository configuration:

```bash
# yum clean all
# yum list all
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
# yum update kernel
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
# chmod 2770 /dir_bersama/kelompok_siswa
```

-   Verify the shared directory configuration:

```bash
# ls -ld /dir_bersama/kelompok_siswa
```

## SSH

-   Create ssh key:

```bash
# ssh-keygen
```

-   copy ssh key

```bash
# ssh-copy-id user@server
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
server time.example.com iburst
```

-   Enable and start the service:

```bash
# systemctl enable chronyd
# systemctl start chronyd
```

-   Turn on the time syncronisation:

```bash
# timedatectl set-ntp yes
```

-   Verify the NTP:

```bash
# chronyc sources -v
# timedatectl
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

-   Enable and start the service:

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

-   Verify:

```bash
# ls -l /root
```

## Extract a Line from a File with String Matching

-   Extract a live in `/etc/passwd` contains `karti` and stores it in a file `/root/user.karti.txt`

```bash
# cat /etc/passwd | grep karti > /root/user.karti.txt
```

-   Verify:

```bash
# ls -l /root
```

## Start Cockpit Service

-   Starting a cockpit service

```bash
# system start cockpit.service
```

-   Open web browser and access the <http://servername:9090>

## Create a New Physical Partition

-   List the partition table

```bash
# lsblk -fp
```

-   Create partition label (`msdos` or `gpt`) on `/dev/sdb` [use `m` for help in `fdisk` menu, don't forget to write the changes using `w`]&#x3A;

```bash
# fdsik /dev/sdb
```

-   Create a new partition on `/dev/sdb` [use `m` for help in `fdisk` menu, don't forget to write the changes using `w`]

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
# lsblk -fp
```

-   Register the UUID into the fstab

```bash
# vim /etc/fstab
```

-   Add this line:

```bash
UUID=<uuid_for_sdb1> /my_mount ext4 defaults 0 0
```

-   Alternatively we can use device name

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

-   List the partition table

```bash
# lsblk -fp
```

-   Create a new partition on `/dev/sdb` [use `m` for help in `fdisk` menu, don't forget to adjust the partition type to `linux-swap` using `t` and also to write the changes using `w`]

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
# lsblk -fp
```

-   Register the UUID into the fstab

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
# free -m
```
