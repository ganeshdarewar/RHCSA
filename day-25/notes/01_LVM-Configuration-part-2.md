ganeshdarewar: create notes of this file

GitHub Copilot: # LVM Configuration Part-2 — Notes

---

## 1. LVM Overview

- **LVM (Logical Volume Manager)** allows flexible disk management.
- Key components:
  - **PV (Physical Volume):** Disk/partition initialized for LVM.
  - **VG (Volume Group):** Pool of storage from PVs.
  - **LV (Logical Volume):** Usable storage carved from VG.

---

## 2. Creating LVM Step-by-Step

### a. Partitioning Disk

- Use `fdisk /dev/sdb` to create partitions.
- Set partition type to `8e` (Linux LVM).
- Example: `/dev/sdb5` created as 190MB for LVM.

### b. Initialize Physical Volume

```bash
pvcreate /dev/sdb5
```

### c. Create Volume Group

```bash
vgcreate -s 8M fedora /dev/sdb5
```
- `-s 8M`: Sets PE (Physical Extent) size to 8MB.

### d. Create Logical Volume

```bash
lvcreate -l 20 -n redhat /dev/fedora
```
- `-l 20`: 20 extents (8MB * 20 = 160MB).

### e. Format and Mount

```bash
mkfs.ext4 /dev/fedora/redhat
mkdir /lvm-4
mount /dev/fedora/redhat /lvm-4
```

### f. Permanent Mount

- Add UUID entry to `/etc/fstab` for `/lvm-4`.

---

## 3. LVM Recovery

### a. If LV is deleted

- Use `vgcfgrestore` to restore from archive:
  - List restore points:
    ```bash
    vgcfgrestore -l fedora
    ```
  - Restore:
    ```bash
    vgcfgrestore -f /etc/lvm/archive/fedora_XXXX.vg fedora
    lvchange -ay /dev/fedora/redhat
    mount /dev/fedora/redhat /lvm-4
    ```

### b. If VG and LV are deleted

- Restore VG config as above.
- Activate LV and mount.

---

## 4. SWAP Creation

### a. During OS Install

- Can be standard or LVM-based.

### b. After OS Install

- **Partition-based:** Create partition, `mkswap`, `swapon`.
- **File-based:** Use `dd` to create file, `mkswap`, `swapon`.

### c. LVM-based SWAP

```bash
lvcreate -L 512M -n myswap /dev/myvg1
mkswap /dev/myvg1/myswap
swapon /dev/myvg1/myswap
```
- Add UUID to `/etc/fstab` for persistence.

---

## 5. Extending LVM-based SWAP

1. **Turn off swap:**
   ```bash
   swapoff /dev/myvg1/myswap
   ```
2. **Resize LV:**
   ```bash
   lvresize -L 1G /dev/myvg1/myswap
   ```
3. **Recreate swap:**
   ```bash
   mkswap /dev/myvg1/myswap
   swapon /dev/myvg1/myswap
   ```

---

## 6. Removing LVMs and VGs

1. **Unmount filesystems:**
   ```bash
   umount /lvm-1 /lvm-2 /lvm-3 /lvm-4
   swapoff /dev/myvg1/myswap
   ```
2. **Remove LVs:**
   ```bash
   lvremove /dev/fedora/redhat
   lvremove /dev/myvg1/mylv1 /dev/myvg1/mylv2 /dev/myvg1/mylv3 /dev/myvg1/myswap
   ```
3. **Remove VGs:**
   ```bash
   vgremove /dev/fedora
   vgremove /dev/myvg1
   ```
4. **Remove PVs:**
   ```bash
   pvremove /dev/sdb1 /dev/sdb2 /dev/sdb3 /dev/sdb5
   ```
5. **Delete partitions with `fdisk` and update kernel with `partprobe`.**
6. **Remove mount points if needed:**
   ```bash
   rm -rf /lvm-1 /lvm-2 /lvm-3 /lvm-4
   ```

---

## 7. Useful Commands

- `lsblk`, `df -Th`, `blkid` — View block devices, filesystems, UUIDs.
- `lvs`, `vgs`, `pvs` — Show LVM status.
- `vgcfgrestore` — Restore VG metadata.
- `lvchange -ay` — Activate LV.
- `swapon -s`, `free -h` — Check swap status.

---

## 8. Notes

- Always update `/etc/fstab` and run `systemctl daemon-reload` after changes.
- LVM archives are stored in `/etc/lvm/archive/` for recovery.
- For swap on LVM, always offline swap before resizing.

---

**End of Notes**