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

- Need a help? Find `/Example:`

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

-   Create users: `tejo`, `bejo`, `wati`:

```bash
# useradd tejo
# useradd bejo
# useradd karti
```

-   Include user `tejo` and `bejo` to `siswa` as their secondary group:

```bash
# usermod -aG siswa tejo
# usermod -aG siswa bejo
```

-   Verify their groups:

```bash
# id tejo
# id bejo
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

-   Active and start GUI

```bash
# systemctl enable graphical.target
# systemctl start graphical.target
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

- Configure `chrony.conf` file:
```bash
# vim /etc/chrony.conf 
```

- Add this entry to the file:

```bash
server time.examplle.com
```

- Enable and restart the service:

```bash
# systemctl enable chronyd
# systemctl restart chronyd
```
- Verify the NTP:
```bash
# chronyc source -v
```