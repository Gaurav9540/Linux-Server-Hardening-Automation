# Linux-Server-Hardening-Automation

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

- permitRootLogin no <br>
- passAuthentication yes <br>
- systemctl restart sshd <br>

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
```shell
yum install fail2ban -y
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
