# Module 3 User & Group Management

**Environment:** Oracle Linux 9 on VirtualBox

---

**User and group management** is a core Linux administration skill. This guide covers creating, modifying, and deleting users and groups.

---

## Key Files

/etc/passwd — User account information  
/etc/shadow — Encrypted passwords  
/etc/group — Group definitions  
/etc/gshadow — Group passwords (rarely used)  
/home/username — Each user's home directory

---

## View Current Users

whoami — Shows the current logged-in user  
id — Shows user ID, group ID, and all groups the user belongs to  
who — Shows who is currently logged into the system  
users — Simple list of logged-in usernames

---

## Creating Users (useradd)

sudo useradd john

This creates user 'john' with default settings. What happens automatically: home directory at /home/john, default group named 'john', shell set to /bin/bash, and skeleton files copied from /etc/skel.

After creating a user, you must set a password with sudo passwd john.

---

## Useradd Options

Option | Meaning | Example
-------|---------|--------
-m | Create home directory | sudo useradd -m john
-d | Specify home directory path | sudo useradd -d /opt/johnhome john
-c | Comment (full name) | sudo useradd -c "John Doe" john
-s | Login shell | sudo useradd -s /bin/bash john
-u | Specific user ID | sudo useradd -u 1500 john
-G | Add to supplementary groups | sudo useradd -G wheel,developers john

Examples:

sudo useradd -m -c "John Developer" -s /bin/bash -u 1501 john

sudo useradd -m -d /opt/student -c "Lab Student" student

---

## Setting and Changing Passwords (passwd)

sudo passwd john

Type the password twice. The password is encrypted and stored in /etc/shadow.

A regular user can change their own password by typing passwd without sudo.

---

## Modifying Users (usermod)

sudo usermod -c "John Smith" john — Change full name/comment  
sudo usermod -s /bin/sh john — Change login shell  
sudo usermod -l johndoe john — Change username  
sudo usermod -L john — Lock account (prevents login)  
sudo usermod -U john — Unlock account  
sudo usermod -aG wheel john — Append user to group 'wheel' (adds without removing other groups)

The -a (append) flag is important — without it, usermod -G removes user from other groups.

---

## Viewing User Information

cat /etc/passwd | grep john

Format: username:x:UID:GID:comment:home:shell

The 'x' means the password is stored in /etc/shadow.

cat /etc/shadow | grep john

Shows password hash, last change date, and expiry information. Only root can read this file.

id john — Shows UID, GID, and groups  
groups john — Shows only group memberships  
ls -la /home/john — View user's home directory contents

---

## Deleting Users (userdel)

sudo userdel john — Deletes user but keeps home directory  
sudo userdel -r john — Deletes user AND home directory (use carefully)

Always use -r if you want to completely remove the user's files. Without -r, the home directory remains orphaned.

---

## Switching Users (su)

su - john — Switch to user john (need their password)  
exit — Return to original user

The dash (-) loads the user's full environment (shell, profile, home directory). Without dash, you switch but keep your current environment.

---

## Groups — Viewing

groups — Show groups your user belongs to  
groups username — Show groups of another user  
cat /etc/group — List all groups on system

---

## Groups — Creating

sudo groupadd developers — Create a group  
sudo groupadd -g 3000 admins — Create with specific GID

Option | Meaning
-------|--------
-g GID | Specify group ID number
-r | Create system group (lower GID)

---

## Groups — Adding Users

sudo usermod -aG developers john

Important: -aG not just -G  
-a = append (don't remove from other groups)  
-G = supplementary groups

Without -a, user is removed from all other groups!

Add to multiple groups at once:

sudo usermod -aG developers,admins,docker john

---

## Groups — Primary vs Supplementary

id john

Output:
uid=1001(john) gid=1001(john) groups=1001(john),1002(developers),1003(docker)

Primary group (gid) — default group for files user creates  
Supplementary groups (groups) — additional groups user belongs to

Change user's primary group:

sudo usermod -g developers john

---

## Groups — Removing Users

sudo gpasswd -d john developers

Removes user 'john' from group 'developers'.

---

## Groups — Modifying and Deleting

sudo groupmod -n devops developers — Rename group  
sudo groupmod -g 2500 developers — Change GID  
sudo groupdel developers — Delete group (cannot delete if it's any user's primary group)

---

## Default Useradd Settings

useradd -D

Shows system defaults:
GROUP=100 — Default group for new users  
HOME=/home — Home directory base path  
INACTIVE=-1 — No password expiry by default  
EXPIRE= — No expiration date set  
SHELL=/bin/bash — Default shell  
SKEL=/etc/skel — Skeleton directory for default files  
CREATE_MAIL_SPOOL=yes — Create mail spool in /var/mail
