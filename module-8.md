# Module 8 File System — Managing Storage (Disks, Partitions, Mounting, Swap, LVM)
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

## Swap Partition

Swap is virtual memory used when RAM is full.

### Check Current Swap

swapon --show

free -h

### Create Swap Partition (type 82)

sudo fdisk /dev/sdb

n - new partition

p - primary

1 - partition number

Enter - default first sector

Enter - default sector

t - change type

82 - Linux swap

w - write

### Format as Swap

sudo mkswap /dev/sdb1

### Enable Swap

sudo swapon /dev/sdb1

### Verify

swapon --show

free -h

### Make Swap Permanent (fstab)

sudo blkid /dev/sdb1

sudo nano /etc/fstab

Add: UUID=your-uuid none swap sw 0 0

### Test

sudo swapon -a

swapon --show

### Disable Swap

sudo swapoff /dev/sdb1

### Remove Swap Partition

sudo swapoff /dev/sdb1

sudo fdisk /dev/sdb

d → 1 → w

---

## LVM (Logical Volume Manager)

LVM allows flexible resizing without repartitioning.

### LVM Layers

Physical Volume (PV) - Raw disk/partition

Volume Group (VG) - Pool of storage

Logical Volume (LV) - Usable partition

### Install LVM

sudo dnf install lvm2 -y

### Prepare Partition (type 8e for LVM)

sudo fdisk /dev/sdb

n → p → 1 → Enter → Enter

t → 8e (Linux LVM)

w

### Create Physical Volume (PV)

sudo pvcreate /dev/sdb1

sudo pvs

sudo pvdisplay

### Create Volume Group (VG)

sudo vgcreate vg_data /dev/sdb1

sudo vgs

sudo vgdisplay vg_data

### Create Logical Volume (LV)

sudo lvcreate -L 500M -n lv_data vg_data

sudo lvs

sudo lvdisplay

LV location: /dev/vg_data/lv_data

### Format and Mount LV

sudo mkfs.ext4 /dev/vg_data/lv_data

sudo mkdir /mnt/lvm_data

sudo mount /dev/vg_data/lv_data /mnt/lvm_data

df -h | grep lvm_data

### Extend Logical Volume

sudo lvextend -L +200M /dev/vg_data/lv_data

sudo resize2fs /dev/vg_data/lv_data

df -h | grep lvm_data

### Permanent Mount for LV

sudo blkid /dev/vg_data/lv_data

sudo nano /etc/fstab

Add: UUID=your-uuid /mnt/lvm_data ext4 defaults 0 0

sudo mount -a

df -h | grep lvm_data

### Remove LVM (Cleanup)

sudo umount /mnt/lvm_data

sudo lvremove /dev/vg_data/lv_data

sudo vgremove vg_data

sudo pvremove /dev/sdb1

---

## Commands Summary

Task | Command
-----|--------
List disks | `lsblk`
List partitions | `fdisk -l`
Partition disk | `sudo fdisk /dev/sdb`
Format ext4 | `sudo mkfs.ext4 /dev/sdb1`
Format xfs | `sudo mkfs.xfs /dev/sdb1`
Create mount point | `sudo mkdir /mnt/data`
Mount | `sudo mount /dev/sdb1 /mnt/data`
Unmount | `sudo umount /mnt/data`
Check mounts | `df -h`
Get UUID | `sudo blkid /dev/sdb1`
Edit fstab | `sudo nano /etc/fstab`
Test fstab | `sudo mount -a`
Delete partition | `sudo fdisk /dev/sdb` → d → w

## Swap Commands Summary

| Task | Command |
|------|---------|
| Check swap status | `swapon --show` |
| Check memory + swap | `free -h` |
| Create swap partition | `sudo fdisk /dev/sdb` (set type to `82`) |
| Format as swap | `sudo mkswap /dev/sdb1` |
| Enable swap | `sudo swapon /dev/sdb1` |
| Disable swap | `sudo swapoff /dev/sdb1` |
| Get UUID for fstab | `sudo blkid /dev/sdb1` |
| Add to /etc/fstab | `UUID=... none swap sw 0 0` |
| Test fstab | `sudo swapon -a` |
| Remove swap partition | `sudo swapoff /dev/sdb1` then `sudo fdisk /dev/sdb` → `d` → `w` |

---

## LVM Commands Summary

Task | Command
-----|--------
Create PV | `pvcreate /dev/sdb1`
List PV | `pvs` or `pvdisplay`
Create VG | `vgcreate vg_name /dev/sdb1`
List VG | `vgs` or `vgdisplay`
Create LV | `lvcreate -L size -n lv_name vg_name`
List LV | `lvs` or `lvdisplay`
Extend LV | `lvextend -L +size /dev/vg/lv`
Resize ext4 FS | `resize2fs /dev/vg/lv`
Remove LV | `lvremove /dev/vg/lv`
Remove VG | `vgremove vg_name`
Remove PV | `pvremove /dev/sdb1`
---
