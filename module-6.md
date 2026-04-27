# Module 6 Repositories, Package Management & System Services
**Environment:** Oracle Linux 9 on VirtualBox
---

## Configuring Repositories

A repository is a storage location from which the system downloads and installs software packages.

### Repository Files Location

/etc/yum.repos.d/

View existing repo files:

ls -la /etc/yum.repos.d/

### View Repositories
dnf repolist           # List enabled repos
dnf repolist all       # List all repos (enabled + disabled)

### .repo File Structure
```
[repo-name]
name=Description of Repository
baseurl=http://example.com/path/to/repo
enabled=1
gpgcheck=1
gpgkey=http://example.com/path/to/gpg-key
```
### Basic Repo Commands
dnf repolist                 # List enabled repos

dnf repolist all             # List all repos

sudo dnf clean all           # Clear cache

sudo dnf makecache           # Rebuild cache

sudo dnf config-manager --set-enabled repo-name   # Enable repo

sudo dnf config-manager --set-disabled repo-name  # Disable repo


### Add EPEL Repository
sudo dnf install epel-release -y

---

## Package Management (yum/dnf)

Basic commands for installing, updating, and removing software.

### Important Note
On modern RHEL-based systems (Oracle Linux 9, Rocky 9, Alma 9), `dnf` is the default. `yum` works as a symlink to `dnf`.

### Basic Commands
yum search package           # Search for a package

yum info package             # Get package details

sudo yum install package -y  # Install a package

sudo yum update package      # Update a specific package

sudo yum update -y           # Update all packages

sudo yum remove package -y   # Remove a package

yum list installed           # List installed packages

yum list available           # List available packages

yum check-update             # Check for available updates

yum provides /path/to/file   # Find which package provides a file

### Group Packages
yum group list               # List available package groups
sudo yum group install "group name" -y   # Install a group

### Cache Management
sudo yum clean all           # Clear all cache
sudo yum makecache           # Rebuild cache

### Practice Example
sudo yum install httpd -y
yum list installed | grep httpd
sudo yum remove httpd -y

---

## System Services (systemctl)

systemctl manages systemd services — programs that run in the background.

### Service States
- active (running) — Service is running
- inactive (dead) — Service is stopped
- enabled — Starts on boot
- disabled — Does not start on boot
- masked — Cannot be started (locked)

### Basic systemctl Commands
systemctl status service       # Check if service is running

sudo systemctl start service   # Start a service

sudo systemctl stop service    # Stop a service

sudo systemctl restart service # Restart a service

sudo systemctl enable service  # Enable on boot

sudo systemctl disable service # Disable on boot

systemctl is-enabled service   # Check if enabled on boot

systemctl list-units --type=service           # List all active services

systemctl list-unit-files --type=service      # List all services with enable status


### Practice with SSH

systemctl status sshd

sudo systemctl stop sshd

systemctl status sshd

sudo systemctl start sshd

sudo systemctl restart sshd

systemctl is-enabled sshd

### Practice with Apache (install first)

sudo yum install httpd -y

systemctl status httpd

sudo systemctl start httpd

systemctl status httpd

sudo systemctl enable httpd

systemctl is-enabled httpd

sudo systemctl stop httpd

sudo yum remove httpd -y

---

## Commands Summary
```
# Repositories
dnf repolist
dnf repolist all
sudo dnf clean all
sudo dnf makecache
ls /etc/yum.repos.d/

# Package Management
yum search <package>
yum info <package>
sudo yum install <package>
sudo yum update <package>
sudo yum remove <package>
yum list installed

#System Services
systemctl status <service>
sudo systemctl start <service>
sudo systemctl stop <service>
sudo systemctl restart <service>
sudo systemctl enable <service>
sudo systemctl disable <service>
systemctl is-enabled <service>
systemctl list-units --type=service
```
---
