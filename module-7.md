# Module 7 System Security — Secure Remote Access (SSH, SCP, SFTP)
**Environment:** Oracle Linux 9 on VirtualBox + Windows 11 (Git Bash)

---

## SSH from Windows to Linux VM

SSH (Secure Shell) allows remote command-line access to a Linux machine.

### Prerequisites
- VM network configured (NAT or Bridged)
- SSH service running on VM
- VM IP address known

### Commands on VM
```
ip a                          # Find VM IP address
systemctl status sshd         # Check SSH service is running
sudo systemctl start sshd     # Start SSH if not running
yum install openssh-server    # If not installed on both Machines u want to connect
```
### Test Connectivity with ping (on Windows Git Bash)
`ping 10.50.79.182`

Expected output: Reply from 10.50.79.182: bytes=32 time<1ms TTL=64

If ping fails:
- Check VM network is Bridged or NAT with port forwarding
- Check VM firewall: sudo firewall-cmd --add-service=ssh --permanent

### Connect from Windows Git Bash

ssh username@vm-ip-address

ssh abdullahfaisal@10.50.79.182

Type 'yes' for host key, then enter password.

### Once connected
whoami

pwd

ls

exit                          # Disconnect

---

## SCP — Secure Copy Protocol

Copy files between Windows and Linux.

### Syntax

scp | source | destination 
----|--------|------------

### Copy Windows → Linux
echo "Hello from Windows" > test.txt

`scp test.txt abdullahfaisal@10.50.79.182:/home/abdullahfaisal/`

### Copy Linux → Windows
` scp abdullahfaisal@10.50.79.182:/home/abdullahfaisal/file.txt . `

### Copy directory recursively
`scp -r myfolder/ abdullahfaisal@10.50.79.182:/home/abdullahfaisal/ `

---

## SFTP — SSH File Transfer Protocol

Interactive file transfer — browse both local and remote systems.

### Connect
sftp abdullahfaisal@10.50.79.182

### SFTP Commands

Command | Purpose
--------|---------
ls | List files on remote (VM)
rm file | Delete file on remote
get file | Download from remote to local
put file | Upload from local to remote
---

## SCP vs SFTP

Feature | SCP | SFTP
--------|-----|------
Best for | Quick single transfers | Interactive browsing
Recursive copy | `-r` flag | `get -r` / `put -r`
Browse remote | No | Yes (ls, cd)
Browse local | No | Yes (lls, lcd)
Speed | Faster | Slightly slower

---

## Commands Summary
```
ping ip-address                # Test connectivity
ssh user@ip                    # Connect to remote
scp local.txt user@ip:         # Upload file
scp user@ip:file.txt .         # Download file
scp -r folder/ user@ip:        # Upload directory
sftp user@ip                   # Start SFTP session
```
---
