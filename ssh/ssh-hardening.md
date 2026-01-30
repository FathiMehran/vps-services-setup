# SSH Hardening on VPS

## Goal
Reduce brute-force attacks and secure SSH access.


## Prerequisites
- Ubuntu VPS
- Root or sudo access
- Existing SSH access


## Actions taken
- Changed default SSH port
- Disabled root login
- Allowed only key-based authentication

## Configuration
File edited:


The following settings were applied in /etc/ssh/sshd_config:

sudo nano /etc/ssh/sshd_config

Key settings:

Apply these settings:

Port 2222

PermitRootLogin no

PasswordAuthentication no


sudo systemctl restart ssh

