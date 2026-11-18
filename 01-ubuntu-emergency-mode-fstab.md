# Lab 01: Recovering Ubuntu from Emergency Mode (Corrupted `/etc/fstab`)

## 1. Scenario & Symptoms
* **Environment:** Ubuntu KVM virtual machine (`libvirt` / `virt-manager`)
* **Symptoms:** After a reboot, the system hung during the boot sequence while waiting for a block device to appear (`A start job is running for /dev/disk/by-uuid/...`) and dropped into **systemd Emergency Mode** (`Press Enter for maintenance`). Network services and SSH were unavailable.

## 2. Root Cause Analysis
Inspecting the systemd journal and `/etc/fstab` revealed an invalid entry referencing a non-existent disk UUID:

```bash
journalctl -xb -p err
cat /etc/fstab
```

The final line of `/etc/fstab` pointed to an unreachable UUID (`UUID=deadbeef-1234-5678-90ab-cdef12345678 /mnt/backup ext4 defaults 0 2`), causing `local-fs.target` to fail during boot.

## 3. Resolution Steps
1. **Remounted the root filesystem as read-write** (Emergency Mode often mounts `/` as read-only):
   ```bash
   mount -o remount,rw /
   ```
2. **Removed the invalid mount entry** from `/etc/fstab`:
   ```bash
   nano /etc/fstab
   # Removed or commented out the invalid UUID=deadbeef... line
   ```
3. **Validated `/etc/fstab` syntax before rebooting:**
   ```bash
   mount -a
   ```
   *(Confirmed exit code `0` with zero mount errors.)*
4. **Rebooted the system** into normal multi-user graphical mode:
   ```bash
   reboot
   ```

## 4. Key Takeaways & Prevention
* Always run `sudo mount -a` immediately after editing `/etc/fstab` to catch typos or missing UUIDs before rebooting a server.
* For non-critical secondary drives (like external backups or network shares), add the `nofail` mount option so a missing drive does not halt the entire OS boot process.
