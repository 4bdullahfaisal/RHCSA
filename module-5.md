# Module 5 Advanced Permissions
**Environment:** Oracle Linux 9 on VirtualBox
---

## Special Permissions (SUID, SGID, Sticky Bit)

Linux has three special permissions beyond standard rwx.

| Permission | Effect | Symbol |
|------------|--------|--------|
| SUID | Run file with owner's permissions | `s` on user `x` |
| SGID | Run with group's permissions (file) OR new files inherit group (dir) | `s` on group `x` |
| Sticky Bit | Only file owner can delete in shared directory | `t` on others `x` |

---

### Sticky Bit (`t`)

Used on directories. Everyone can create files, but only file owner, directory owner, or root can delete.

Example - /tmp directory:
ls -ld /tmp
Output: drwxrwxrwt (the t at the end is sticky bit)

Practice:
sudo mkdir /shared
sudo chmod 777 /shared
sudo chmod +t /shared
ls -ld /shared

Now other users cannot delete each other's files in /shared.

Remove sticky bit:
sudo chmod -t /shared

---

### SUID (Set User ID)

Used on executable files. Runs with owner's privileges, not the user running it.

Example - /bin/passwd (updates /etc/shadow as root):
ls -l /bin/passwd
Output: -rwsr-xr-x (s where x should be for user)

Practice on a binary:
sudo cp /bin/sleep /usr/local/bin/sleep-suid
sudo chmod u+s /usr/local/bin/sleep-suid
ls -l /usr/local/bin/sleep-suid

---

### SGID (Set Group ID)

On files: runs with group owner's privileges
On directories: new files inherit directory's group

Practice on directory:
sudo mkdir /group-shared
sudo chmod 2775 /group-shared
ls -ld /group-shared
Output: drwxrwsr-x (s in group x)

---

### Special Permission Numbers (4-digit chmod)

Number | Permission
-------|-----------
4 | SUID
2 | SGID
1 | Sticky Bit

Examples:
chmod 4755 file   # -rwsr-xr-x (SUID)
chmod 2755 dir    # drwxr-sr-x (SGID)
chmod 1755 dir    # drwxr-xr-t (Sticky)
chmod 3755 dir    # drwxr-sr-t (SGID + Sticky)

---

## ACL (Access Control Lists)

ACL allows giving permissions to specific users or groups beyond standard owner/group/others.

### Why ACL?

Standard Linux permissions only allow:
- One owner
- One group
- Others

ACL lets you give access to multiple specific users or groups.

### Basic ACL Commands

Command | Purpose
--------|---------
`getfacl filename` | View ACL of file/directory
`setfacl -m u:username:perms filename` | Give specific user permissions
`setfacl -m g:groupname:perms filename` | Give specific group permissions
`setfacl -x u:username filename` | Remove user's ACL entry
`setfacl -b filename` | Remove all ACLs

### ACL Syntax

Type | Syntax | Example
-----|--------|---------
User | `u:username:perms` | `u:john:rwx`
Group | `g:groupname:perms` | `g:devops:r-x`
Mask | `m:perms` | `m:rwx`
Other | `o:perms` | `o:r--`
Default | `d:u:user:perms` | `d:u:john:rwx`

Permissions: r (read), w (write), x (execute), - (none)

## Commands Summary

Sticky Bit: `chmod +t directory`
SUID: `chmod u+s file`
SGID: `chmod g+s file` or `chmod 2775 directory`

ACL:
`getfacl file`
`setfacl -m u:user:perms file`
`setfacl -m g:group:perms file`
`setfacl -x u:user file`
`setfacl -b file`

Special chmod numbers:
4755 - SUID
2755 - SGID
1777 - Sticky Bit

---
