ganeshdarewar: create notes of this file

GitHub Copilot: Here are concise notes summarizing the key steps and concepts from your **LVM-Configuration-Notes.txt** file:

---

# LVM Configuration Notes

## 1. Disk Preparation
- Use `lsblk` and `fdisk -l` to list disks and partitions.
- Create new partitions with `fdisk` or `parted`.
- Set partition type to `8e` (Linux LVM).
- Use `partprobe` to update the kernel with new partition info.

## 2. LVM Setup

### a. Create Physical Volume (PV)
```bash
pvcreate /dev/sdb1
pvs
pvdisplay
```

### b. Create Volume Group (VG)
```bash
vgcreate myvg1 /dev/sdb1
vgs
vgdisplay
```

### c. Create Logical Volume (LV)
```bash
lvcreate -L 1G -n mylv1 myvg1
lvcreate -l 125 -n mylv2 myvg1
lvs
lvdisplay
```

## 3. Filesystem Creation & Mounting
- Format LVs:
  - XFS: `mkfs.xfs /dev/myvg1/mylv1`
  - EXT4: `mkfs.ext4 /dev/myvg1/mylv2`
- Create mount points: `mkdir /lvm-1 /lvm-2`
- Mount LVs:  
  `mount /dev/myvg1/mylv1 /lvm-1`  
  `mount /dev/myvg1/mylv2 /lvm-2`
- Add to `/etc/fstab` using device paths or UUIDs.

## 4. Testing LVMs
- Copy data to test: `cp -rf /etc /lvm-1`
- Check usage: `df -Th`, `du -sh /lvm-1`

## 5. Extending VG and LV
- Add new partition, create PV, extend VG:
  ```bash
  pvcreate /dev/sdb2
  vgextend myvg1 /dev/sdb2
  ```
- Extend LV and filesystem:
  - XFS:  
    `lvextend -L +200M /dev/myvg1/mylv1`  
    `xfs_growfs /dev/myvg1/mylv1`
  - EXT4:  
    `lvextend -L +200M /dev/myvg1/mylv2`  
    `resize2fs /dev/myvg1/mylv2`
  - Or use `lvresize -L <size> -r <LV>` for both steps.

## 6. Reducing LV Size (EXT4 only)
- Unmount LV, shrink filesystem, then reduce LV:
  ```bash
  lvresize -L 300M -r /dev/myvg1/mylv2
  ```
- XFS cannot be shrunk directly; use backup/restore method.

## 7. Deactivating/Activating LVMs
- Deactivate:  
  `umount /lvm-2`  
  `lvchange -an /dev/myvg1/mylv2`
- Reactivate:  
  `lvchange -ay /dev/myvg1/mylv2`

## 8. Merging Volume Groups
- Deactivate LV and VG:
  ```bash
  umount /lvm-3
  lvchange -an /dev/myvg2/mylv3
  vgchange -an /dev/myvg2
  ```
- Merge:  
  `vgmerge /dev/myvg1 /dev/myvg2`
- Reactivate and mount as needed.

## 9. Filesystem Backup/Restore (XFS)
- Backup:  
  `xfsdump -f /tmp/test.dump /lvm-1`
- Restore:  
  `xfsrestore -f /tmp/test.dump /lvm-1`

## 10. Useful Commands
- `lsblk`, `blkid`, `df -Th`, `du -sh`
- `pvs`, `vgs`, `lvs`
- `pvcreate`, `vgcreate`, `lvcreate`
- `lvextend`, `lvresize`, `lvreduce`
- `mkfs.xfs`, `mkfs.ext4`, `xfs_growfs`, `resize2fs`
- `lvchange`, `vgchange`, `vgmerge`

---

**Note:**  
- XFS filesystems cannot be shrunk directly.
- Always backup data before reducing LVM sizes.
- Use UUIDs in `/etc/fstab` for persistent mounts.

---

This summary covers the main LVM operations and concepts from your notes. Let me know if you need a more detailed or step-by-step guide for any section!