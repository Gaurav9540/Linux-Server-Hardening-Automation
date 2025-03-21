# Linux-Server-Hardening-Automation

1. Launch EC2 Instance and Connect
  
2. Update Packages
```shell
yum update -y
```

3. Create User and set Password to the User
```shell
adduser <username>
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

permitRootLogin no <br>
passAuthentication yes <br>
systemctl restart sshd <br>

6. Firewall Setup
```shell
yum install firewalld -y
```

```shell
systemctl enable firewalld
```
