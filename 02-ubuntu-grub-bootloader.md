# Lab 02: Manual Boot Recovery from the `grub>` Rescue Shell

## 1. Scenario & Symptoms
* **Environment:** Ubuntu KVM virtual machine (`libvirt` / `virt-manager`)
* **Symptoms:** On reboot, the virtual machine failed to load the operating system boot menu and dropped directly into an interactive **`grub>`** shell prompt (`error: unknown command 'corrupted_grub_config'`).

## 2. Root Cause Analysis
Inspecting the boot partition from the GRUB shell revealed that `/boot/grub/grub.cfg` had been overwritten with invalid syntax, preventing GRUB from locating the kernel and initramfs automatically.

## 3. Resolution Steps
1. **Identified the Linux root partition** from the `grub>` prompt by listing available block devices and inspecting directory contents:
   ```text
   grub> ls
   grub> ls (hd0,gpt2)/
   ```
2. **Manually loaded the Linux kernel and initramfs** into memory and booted the system:
   ```text
   grub> set root=(hd0,gpt2)
   grub> linux /boot/vmlinuz-<version>-generic root=/dev/vda2
   grub> initrd /boot/initrd.img-<version>-generic
   grub> boot
   ```
3. **Regenerated a clean GRUB configuration** once the OS booted into multi-user mode:
   ```bash
   sudo update-grub
   sudo rm -f /boot/grub/grub.cfg.bak
   ```
4. **Verified persistence** by performing a full `sudo reboot` and confirming Ubuntu booted cleanly without manual intervention.

## 4. Key Takeaways & Prevention
* Even when `/boot/grub/grub.cfg` is corrupted or deleted, a system can be booted directly from the `grub>` shell as long as the kernel (`vmlinuz`) and initial RAM disk (`initrd.img`) remain intact on `/boot`.
* Always back up `/boot/grub/grub.cfg` before editing bootloader parameters, and prefer configuring `/etc/default/grub` followed by `sudo update-grub` rather than editing `grub.cfg` directly.
