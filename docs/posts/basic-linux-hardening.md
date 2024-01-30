---
draft: false
date: 2024-01-05
authors:
  - me
categories:
  - Linux
---

# Basic Linux Server Setup & Hardening
Here are initial steps I perform on any fresh Linux install. My preferred distro is Debian, so anything below is within that context.

<!-- more -->

## Perform updates, set hostname, add non-root users
Perform updates
```bash
apt update && apt upgrade -y
```
Configure hostname
```bash
hostnamectl set-hostname debian
#
# vim /etc/hosts and add following line
# <ip-address> debian
#
```
Add non-root users
```bash
adduser my-username
adduser my-username sudo
```

## Configure SSH
In order to secure SSH, I temporarily enable password auth to copy a non-root public key over. Once copied, I disable password auth, and disable root from authenticating via ssh

1. Temporarily enable password auth
```bash
# /etc/ssh/sshd_config
Include /etc/ssh/sshd_config.d/*.conf
PermitRootLogin yes
PasswordAuthentication yes
```
Restart sshd with: 
```bash
systemctl restart sshd
```

2. From client machine, copy-id for non-root account from client host
```bash
ssh-copy-id my-username@<ip-address>
```

3. Reconfigure ssh to disable password auth, and disable remote root authentication
```bash
# /etc/ssh/sshd_config
Include /etc/ssh/sshd_config.d/*.conf
PermitRootLogin no
PasswordAuthentication no
```
Restart sshd with: `systemctl restart sshd`

4. Lastly, enable passwordless sudo

```bash
# update default visudo editor to vim
update-alternatives --config editor

# use visudo to edit /etc/sudoers file
visudo

%sudo    ALL=(ALL) NOPASSWD:ALL
```

## Secure server with host firewall UFW
Permit outbound traffic, deny all inbound traffic except SSH:
```bash
sudo apt install ufw
sudo ufw default allow outgoing
sudo ufw default deny incoming
sudo ufw allow ssh
sudo ufw enable
sudo ufw status
```
