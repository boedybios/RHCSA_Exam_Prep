# RHCSA Exam Preparation

## Reset Root Password -> Super Important

-   Reboot the system
-   Enter Grub Edit menu by pressing `e`
-   Find `linux16` or `linuxefi` line
-   In the end of the line, add this: 

```bash
rd.break selinux=0
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

-   Exit the shell and reboot: 

```bash
# reboot
```

## Configure Network and Hostname

-   For interactive way to configure both network and hostname is using `nmtui`

```bash
# nmtui
```

-   Test the connection using `ping`

```bash
# ping example.com
```

## Configure SELinux for Enforcing Mode

-   Open SELinux config file: 

```bash
# vim /etc/selinux/config
```

-   Enable this line:

```bash
SELINUX=enforcing
```

-   Reboot the system: 

```bash
# reboot
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
```

-   Test the repo configuration:

```bash
# yum clean all
# yum list all
# yum repolist
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

## Create A Shared Directory

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
# chmod g+s /dir_bersama/kelompok_siswa
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

## Update Kernel

-   Add the appropriate kernel repository

```bash
# vim /etc/yum.repos.d/REPO_NAME.repo
```

```bash
[kernelrepo]
name = kernel repository
baseurl = http://server.example.com/kernel_repo
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
server time.examplle.com
```

-   Enable and start the service:

```bash
# systemctl enable chronyd
# systemctl start chronyd
```

-   Verify the NTP:

```bash
# chronyc source -v
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
