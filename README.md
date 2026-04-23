# Linux Shell Trainees Task

A complete Linux system administration lab covering real-world tasks across 11 parts — from disk management and user administration to web-based system monitoring. All commands were executed and verified on a RHEL/CentOS-based Linux system.

---

## Table of contents

- [Overview](#overview)
- [Parts covered](#parts-covered)
- [Technologies used](#technologies-used)
- [How to use this repo](#how-to-use-this-repo)

---

## Overview

This repository documents my full solutions for a multi-part Linux and Shell administration lab. Each part targets a specific sysadmin skill area, building toward a complete, production-style Linux environment with users, services, monitoring, and a live web dashboard.

---

## Parts covered

### Part 1 — LVM (Logical Volume Manager)
Disk partitioning, physical/volume group creation with 16M PE size, logical volume setup, filesystem mounting, and persistent `fstab` configuration.

```bash
fdisk /dev/sdb
pvcreate /dev/sdb1
vgcreate -s 16M vgdata /dev/sdb1
lvcreate -n lv -l 100%VG vgdata
mount /dev/vgdata/lv /mnt/data
```

---

### Part 2 — Users, groups & permissions
Created users with custom UIDs and login restrictions, configured groups, assigned sudo privileges, and verified access control.

```bash
useradd -u 601 -s /sbin/nologin user1
groupadd TrainingGroup
usermod -aG wheel user3
```

---

### Part 3 — SSH key-based authentication
Generated RSA 4096-bit key pair and configured passwordless SSH login to a remote server.

```bash
ssh-keygen -t rsa -b 4096
ssh-copy-id ldap@ipaserver.example.com
```

---

### Part 4 — File permissions
Assigned file ownership and restricted access using `chmod` and `chown`.

```bash
cp /etc/fstab /var/tmp/admin
chown user1:user1 /var/tmp/admin
chmod 700 /var/tmp/admin
```

---

### Part 5 — SELinux
Enabled and enforced SELinux permanently via config file and runtime command.

```bash
setenforce 1
# /etc/selinux/config → SELINUX=enforcing
```

---

### Part 6 — Bash script & process management
Wrote a monitoring script (`monitor_process.sh`) that launches a background process, monitors it in a loop for 600 seconds, and kills it when done.

```bash
sleep 600 &
# Script monitors PID and kills if still running after timeout
```

---

### Part 7 — Yum / DNF repository
Installed packages, created a local Zabbix repository, disabled external repos, and installed Zabbix server/agent from the local repo.

```bash
createrepo /var/www/html/zabbix
dnf config-manager --disable \*
dnf config-manager --enable local-zabbix
dnf install zabbix-server-mysql zabbix-web-mysql zabbix-agent
```

---

### Part 8 — Firewall & network management
Opened HTTP/HTTPS services and blocked SSH access from a specific IP using rich rules.

```bash
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.100" port protocol="tcp" port="22" reject'
firewall-cmd --reload
```

---

### Part 9 — Cron job
Created a shell script to log currently logged-in users and scheduled it to run daily at 01:30 via crontab.

```bash
# crontab entry
30 1 * * * /usr/local/bin/log_users.sh
```

---

### Part 10 — MariaDB
Installed MariaDB, created a database and table, inserted 10 records, and configured a dedicated database user with remote access.

```sql
CREATE DATABASE studentdb;
CREATE USER 'studentuser'@'%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON studentdb.* TO 'studentuser'@'%';
```

---

### Part 11 — Web dashboard for system statistics
Built a live system statistics dashboard served via Apache. Cron-scheduled shell scripts collect and calculate averages for CPU, memory, and disk usage, writing results to HTML pages.

```bash
# Collection script runs every minute
* * * * * /root/collect_stats.sh

# Average calculation runs every hour
0 * * * * /root/calculate_avg.sh
```

Access the dashboard:
```bash
curl http://localhost/sysstats/
curl http://localhost/sysstats/cpu.html
curl http://localhost/sysstats/memory.html
curl http://localhost/sysstats/disk.html
```

---

## Technologies used

| Area | Tools |
|---|---|
| Storage | LVM, fdisk, fstab |
| Users & access | useradd, groupadd, sudo, chmod, chown |
| Remote access | SSH, RSA key pairs |
| Security | SELinux, firewalld |
| Automation | Bash scripting, cron |
| Package management | DNF, Yum, createrepo |
| Database | MariaDB / MySQL |
| Web server | Apache (httpd) |
| Monitoring | Zabbix, custom stats dashboard |

---

## How to use this repo

Each part is self-contained. You can follow the commands in order to reproduce the full lab environment, or jump to any specific part you need.

> All tasks were tested on a RHEL/CentOS-based distribution. Some commands may differ slightly on Debian/Ubuntu systems.
