# Linux-Server-Hardening-Automation

The Linux Server Hardening & Automation project focuses on securing a Linux-based server by implementing industry-standard security practices, configuring access controls, and setting up automated maintenance and monitoring tasks. The goal is to protect the server from unauthorized access, mitigate vulnerabilities, ensure consistent uptime, and reduce human error through automation.

🔒 **Hardening:**
 - Secure SSH access. <br>

 - Set up firewalls (UFW) and intrusion protection (Fail2Ban). <br>

 - Enable logging and auditing with Auditd. <br>

⚙️ **Automation:**
 - Automate backups (rsync), updates, and log rotation (logrotate) using Cron jobs. <br>

 - Set up health monitoring and automated alerts. <br>

🚀 **Goal:**
 To create a secure, stable, and self-maintaining Linux server for better performance and protection against attacks. 😎


1. Launch EC2 Instance and Connect
  
2. Update Packages
```shell
yum update -y
```


3. Create User and set Password to the User
```shell
adduser <username>
```
```shell
passwd <username>
```


4. Update Root User Password
```shell
passwd
```


5. Disable Root Login
```shell
vim /etc/ssh/sshd_config
```

- permitRootLogin yes <br>
- passAuthentication yes <br>

```shell
systemctl restart sshd 
```

6. Firewall Setup
```shell
yum install firewalld -y
```

```shell
systemctl enable --now firewalld
```

```shell
systemctl status firewalld
```

Add services in firewall
SSH, HTTP, HTTPS

```shell
firewall-cmd --permanent --add-service=SSH
```

```shell
firewall-cmd --permanent --add-service=HTTP
```

```shell
firewall-cmd --permanent --add-service=HTTPS
```

```shell
firewall-cmd --reload
```

```shell
firewall-cmd --list-all
```


7. Install and configure fail2ban
   
**fail2ban:** Fail2Ban is a log-based intrusion prevention tool for Linux that helps protect servers from brute-force attacks and other malicious login attempts. It works by monitoring log files (like /var/log/auth.log) for suspicious activity and automatically banning IP addresses that show signs of attack (e.g., multiple failed login attempts).
 
```shell
yum install fail2ban -y
```

start and enable fail2ban
```shell
systemctl start fail2ban
```

```shell
systemctl enable fail2ban
```

edit conf file and add content
```shell
vim /etc/fail2ban/jail.local
```

add content to the jail.local file
```
# Fail2Ban configuration file (local overrides)
# This file contains custom settings that won't be overwritten by updates.
# The main configuration file is /etc/fail2ban/jail.conf.
#
# DO NOT modify jail.conf directly; use this jail.local file instead.
# For more details, check man 5 jail.conf.

[DEFAULT]
# Ban duration: IP is banned for 1 hour (3600 seconds)
bantime = 3600

# Time window: Count failed attempts within 10 minutes (600 seconds)
findtime = 600

# Maximum failed attempts before ban
maxretry = 3

# Email notifications (optional: set your email for alerts)
destemail = root@localhost
sender = fail2ban@yourserver.com
mta = sendmail

# Ban action (default: iptables)
banaction = iptables-multiport

# Log level (INFO for debugging, WARNING for normal operation)
loglevel = INFO

# Log target (default: syslog, change if needed)
logtarget = /var/log/fail2ban.log

[sshd]
enabled = true
```

Reset if Already IP Shows (OPTIONAL)
```ssh
fail2ban-client unban --all
```

```ssh
systemctl stop fail2ban
fail2ban-client stop
systemctl start fail2ban
fail2ban-client start
```

```ssh
systemctl restart fail2ban
```

```ssh
fail2ban-client status sshd
```


8. Configure System

**auditd:** 
Auditd is the Linux Audit Daemon that provides detailed auditing of security-relevant events on a Linux system. It helps monitor and record various activities such as system calls, file accesses, user logins, and configuration changes. Auditd is useful for compliance, security monitoring, and forensic analysis.

```ssh
yum install audit -y
```

```ssh
systemctl enable --now auditd
```

```ssh
auditctl -w /etc/passwd -p wa -k passwd_changes
```

-w /etc/passwd: -w means watch on /etc/passwd critical file that stores user account information on a Linux system   <br>
-p wa: -p option specifies the permissions to monitor.  <br>

w: Watch for write operations (modifications) to the file.   <br>
a: Watch for attribute changes (e.g., changes to file metadata like permissions).   <br>
Together, wa means to monitor both write and attribute changes to /etc/passwd.   <br>

-k passwd_changes: key to search filter and find all events related to changes in the /etc/passwd file. <br>

```ssh
auditctl -w /etc/shadow -p wa -k shadow_changes
```

9. Configure Automated Backups
```ssh
vim /usr/local/bin/backup.sh
```

```ssh
#!/bin/bash
# create backup
tar -czf /backup/server_backup_$(date +%f).tar.gz /etc /home /var/log

# delete older backup older that 7 days
find /backup -type f -name "server_backup_*.tar.gz" -mtime +7 -delete
```

```ssh
chmod +x /usr/local/bin/backup.sh
```


10. Shedule Cron Job
```ssh
yum install cronie
```

```ssh
systemctl start crond
```

```ssh
systemctl status crond
```

```ssh
crontab -e
```

```ssh
0 2 * * *  /usr/local/bin/backup.sh
```

```ssh
crontab -l
```


11. Testing Through Another User
1.1 Install Firewall
```ssh
yum install firewall -y
```

1.2 Start & Enable Firewall
```ssh
systemctl enable --now firewalld
```

```ssh
systemctl status firewall
```

```ssh
firewall-cmd --list-all
```

1.3 Try to Take SSH of Targeted Machine
```ssh
ssh root@<root password>
```


12. Check fail2ban Status of First Machine
```ssh
fail2ban-client status sshd
```

13. Unban IP
```ssh
fail2ban-client set sshd unbanip <ip address to unban>
```

13. Check fail2ban Status of First Machine
```ssh
fail2ban-client status sshd
```

14. Check Logs
```ssh
cat /var/log/fail2ban.log
```
