# Course Notes for Proxmox VE Essential Training

This document contains notes and links for the course _Proxmox VE Essential Training_, which is available on LinkedIn Learning.

Be sure to bookmark the [Proxmox VE Administration Guide](https://pve.proxmox.com/pve-docs/pve-admin-guide.html)!

Explore the [Proxmox VE Subscription Information](https://www.proxmox.com/en/products/proxmox-virtual-environment/pricing) if you need a support solution.

To get started, [download the Proxmox VE ISO](https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso/proxmox-ve-8-4-iso-installer) and then use it to create an installer disk with a USB drive using `dd`, [Balena Etcher](https://etcher.balena.io/#download-etcher), or [Rufus](https://rufus.ie/en/).

Get ready to start installing operating systems:
- [Download Windows VirtIO Drivers ISO](https://pve.proxmox.com/wiki/Windows_VirtIO_Drivers)
- [Download Ubuntu Server ISO](https://ubuntu.com/download/server)
- [Download Windows 11 ISO](https://www.microsoft.com/en-us/software-download/windows11)

If you're interested, check out [Proxmox VE Administration Guide: Appendix A: Command-line interface](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#_command_line_interface).

Working with PCI Passthrough:
- [Proxmox VE Administration Guide: 10.9. PCI(e) Passthrough](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#qm_pci_passthrough)
- [[TUTORIAL] - PCI/GPU Passthrough on Proxmox VE 8 : Installation and configuration](https://forum.proxmox.com/threads/pci-gpu-passthrough-on-proxmox-ve-8-installation-and-configuration.130218/)

Below are the hardware passthrough steps for my GPU on my host, based on the tutorial linked above. 
***Your details will differ***.

```sh
# Enabling GPU passthrough for my NVIDIA GeForce GT 710
# on an HP Z6 workstation running Proxmox VE 8.4.0 with
# a single-disk installation using Grub.
# Ref: https://forum.proxmox.com/threads/pci-gpu-passthrough-on-proxmox-ve-8-installation-and-configuration.130218/
 
# NOTE: You will need to do your own research to determine 
# which steps suit your system and hardware.

# I have enabled (IOMMU / VT-d) in my BIOS.

# My system uses Grub bootloader.
# Add this to /etc/default/grub:
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt"

# Update grub with the new configuration and reboot.
update-grub
systemctl reboot

# Add these lines to /etc/modules. VFIO is Virtual Function I/O,
# which allows direct access to I/O devices from userspace.
vfio
vfio_iommu_type1
vfio_pci

# Update initramfs with new modules and reboot.
update-initramfs -u -k all
systemctl reboot

# Just to be safe per the guide, set options in 
# /etc/modprobe.d/kvm.conf:
options kvm ignore_msrs=1 report_ignored_msrs=0

# To get device IDs per the guide:
lspci -nn | grep 'NVIDIA'

# My device IDs are 10de:128b and 10de:0e0f.
# So, set the order of modules per the guide in 
# /etc/modprobe.d/vfio.conf.
# This ensures that the vfio-pci module loads first
# and is used to control the PCI devices with the
# provided IDs.
options vfio-pci ids=10de:128b,10de:0e0f
softdep nouveau pre: vfio-pci
softdep nvidia pre: vfio-pci
softdep nvidiafb pre: vfio-pci
softdep nvidia_drm pre: vfio-pci
softdep drm pre: vfio-pci

# Reboot again.
systemctl reboot
```

Learn about [Virtiofs support in Windows guests](https://github.com/virtio-win/kvm-guest-drivers-windows/wiki/Virtiofs:-Shared-file-system).

LinkedIn Learning courses for further exploration:
- [Ubuntu Linux: Essential Commands and System Administration](https://www.linkedin.com/learning/ubuntu-linux-essential-commands-and-system-administration)
- [Ubuntu Linux: Providing Services on LinkedIn Learning](https://www.linkedin.com/learning/ubuntu-linux-providing-services)
- [Learning SFTP on LinkedIn Learning](https://www.linkedin.com/learning/learning-sftp-29541201)
- [Virtualization with KVM and Qemu](https://www.linkedin.com/learning/virtualization-with-kvm-and-qemu)

Additional links:
- [Proxmox Wiki: VM Templates and Clones](https://pve.proxmox.com/wiki/VM_Templates_and_Clones)
- [Proxmox Backup Server](https://www.proxmox.com/en/products/proxmox-backup-server/overview)

Details on Software Defined Networking in PVE: [Software-Defined Network](https://pve.proxmox.com/wiki/Software-Defined_Network)

Private NAT Network: [Setup Simple Zone With SNAT and DHCP](https://pve.proxmox.com/wiki/Setup_Simple_Zone_With_SNAT_and_DHCP)

Explore the [Ceph Documentation](https://docs.ceph.com/en/reef/).

Explore how to [migrate to Proxmox VE](https://pve.proxmox.com/wiki/Migrate_to_Proxmox_VE) from other hypervisor products.

Explore the useful [Proxmox VE Helper-Scripts](https://community-scripts.github.io/ProxmoxVE/scripts).
