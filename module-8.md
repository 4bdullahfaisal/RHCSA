# Module 8 File System — Managing Storage (Disks, Partitions, Mounting)
**Environment:** Oracle Linux 9 on VirtualBox

---

## Storage Management Overview

| Step | What it does |
|------|--------------|
| 1. Add a new disk | Attach virtual disk to VM |
| 2. Partition | Divide disk into sections |
| 3. Format | Create filesystem (ext4, xfs) |
| 4. Mount | Attach partition to folder |
| 5. Permanent mount | Configure /etc/fstab |
| 6. Delete partition | Remove partition using fdisk |

---

## Step 1 — Add New Disk in VirtualBox

1. Shut down VM: `sudo shutdown now`
2. In VirtualBox → Settings → Storage
3. Click Add Hard Disk → Create new disk → VDI → Dynamically allocated
4. Size: 2GB (for practice)
5. Start VM

---

## Step 2 — Verify New Disk

lsblk

You'll see:
- sda — existing disk
- sdb — new 2GB disk

Also check:
fdisk -l

---

## Step 3 — Partition the Disk

sudo fdisk /dev/sdb

Inside fdisk commands:
```
n  — New partition
p  — Primary partition
1  — Partition number 1
Enter — First sector (default)
Enter — Last sector (use full disk)
w  — Write changes and exit
```
Verify:

lsblk

sudo fdisk -l /dev/sdb

Now you have /dev/sdb1

---

## Step 4 — Format with Filesystem

Format as ext4:

sudo mkfs.ext4 /dev/sdb1

Or format as XFS:

sudo mkfs.xfs /dev/sdb1

---

## Step 5 — Create Mount Point and Mount

sudo mkdir /mnt/data

sudo mount /dev/sdb1 /mnt/data

---

## Step 6 — Verify Mount

df -h

mount | grep sdb1

lsblk

---

## Step 7 — Permanent Mount (Edit /etc/fstab)

Get UUID:

sudo blkid /dev/sdb1

Copy the UUID (e.g., UUID="abc123...")

Edit fstab:

sudo nano /etc/fstab

Add this line:

UUID=abc123... /mnt/data ext4 defaults 0 0

Save: Ctrl+O, Enter, Ctrl+X

Test fstab:

sudo mount -a

df -h

If no errors, fstab is correct.

---

## Step 8 — Unmount Partition

sudo umount /dev/sdb1

# or

sudo umount /mnt/data

Verify:

df -h | grep sdb1

---

## Step 9 — Delete Partition

Open fdisk:

sudo fdisk /dev/sdb

Inside fdisk:

d  — Delete partition

1  — Partition number (if asked)

w  — Write changes and exit

Verify:

lsblk

sdb1 should be gone.

If partition was mounted, unmount first:

sudo umount /dev/sdb1

---

## Commands Summary

Task | Command
-----|--------
List disks | `lsblk` or `fdisk -l`
Partition | `sudo fdisk /dev/sdb`
Format ext4 | `sudo mkfs.ext4 /dev/sdb1`
Format xfs | `sudo mkfs.xfs /dev/sdb1`
Create mount point | `sudo mkdir /mnt/data`
Mount | `sudo mount /dev/sdb1 /mnt/data`
Check mounts | `df -h`
Get UUID | `sudo blkid /dev/sdb1`
Edit fstab | `sudo nano /etc/fstab`
Test fstab | `sudo mount -a`
Unmount | `sudo umount /mnt/data`
Delete partition | `sudo fdisk /dev/sdb` → d → w

---

## /etc/fstab Fields Explained

Field | Meaning
------|--------
1 | Device (UUID or /dev/sdb1)
2 | Mount point (e.g., /mnt/data)
3 | Filesystem type (ext4, xfs)
4 | Options (defaults)
5 | Dump backup (0 = no)
6 | Filesystem check order (0 = skip)

---

---

**All commands practiced in Oracle Linux VM inside VirtualBox.**

End of Day 8
