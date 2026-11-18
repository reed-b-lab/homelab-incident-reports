# homelab-incident-reports-
Hands-on Linux sysadmin &amp; security break/fix labs documented as structured incident reports (KVM/libvirt, Ubuntu, Kali).

# Home Lab Incident Reports & Break/Fix Labs

A collection of hands-on Linux system administration, recovery, and security troubleshooting reports conducted in an isolated KVM/libvirt virtual lab.

Each report documents a realistic failure scenario—from initial symptoms and log analysis to root-cause resolution and preventative best practices.

## Lab Index


| # | Incident / Lab Scenario | System | Key Skills & Tools | Report |
| :--- | :--- | :--- | :--- | :--- |
| **01** | **Emergency Mode Boot Failure** (`Corrupted /etc/fstab`) | Ubuntu KVM Guest | `systemd`, `journalctl`, `mount`, `/etc/fstab` recovery | [View Report](01-ubuntu-emergency-mode-fstab.md) |
| **02** | **GRUB Bootloader Rescue** (`Corrupted grub.cfg`) | Ubuntu KVM Guest | `GRUB`, `vmlinuz`, `initrd`, `update-grub` | [View Report](02-ubuntu-grub-bootloader.md) |


## Lab Methodology

1. **Snapshot Baseline:** Capture a clean hypervisor state (`virsh snapshot-create-as`) before introducing faults.
2. **Fault Injection:** Simulate realistic production outages (broken boot configs, filesystem exhaustion, firewall lockouts, or active brute-force traffic).
3. **Console Diagnosis:** Troubleshoot strictly via local TTY (`virt-viewer` / `virt-manager`) and system logs without relying on hypervisor snapshots to bail out.
4. **Verification & Documentation:** Confirm full service recovery and write a structured post-mortem covering root cause and prevention.

## Environment

* **Hypervisor:** Debian Linux running KVM/QEMU (`libvirt`)
* **Lab Network:** Isolated virtual bridge (`virbr1`) with NAT access (`virbr0`)
* **Guest Systems:** Ubuntu Linux, Kali Linux, Windows 11
