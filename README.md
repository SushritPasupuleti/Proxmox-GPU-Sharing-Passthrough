# Proxmox GPU Sharing Passthrough

Share one GPU with any number of VMs created using Proxmox

## Note

> Gave up on this because of the dependency on the proprietary driver.


## Setup

Add sources

```bash
nano /etc/apt/sources.list
```

in Nano add:

(For v6.x)

```bash
deb http://download.proxmox.com/debian/pve buster pve-no-subscription
```

(For v7.x)

```bash
deb http://download.proxmox.com/debian/pve bullseye pve-no-subscription
```

### Installing Dependencies

```bash
apt update
apt -y upgrade
apt -y install git build-essential pve-headers dkms jq mdevctl

pip3 install frida
```

Get Scripts

```bash
git clone https://github.com/DualCoder/vgpu_unlock
git clone https://github.com/mbilker/vgpu_unlock-rs
```

Install Rust

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

> Select 1

Headers Install

```bash
wget http://download.proxmox.com/debian/dists/bullseye/pve-no-subscription/binary-amd64/pve-headers-5.13.19-2-pve_5.13.19-4_amd64.deb

dpkg -i pve-headers-5.13.19-2-pve_5.13.19-4_amd64.deb
```

Clutch Moment - Get Nvivida KVM Drivers from the official site and download them to CWD.

### Enable IOMMU

Edit Grub

```bash
nano /etc/default/grub
```

Comment out:

```grub
GRUB_CMDLINE_LINUX_DEFAULT="quiet"
```

Based on your CPU Vendor (intel/amd), add:

```grub
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt"
#	- OR -
GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on iommu=pt"
```

Update Grub

```bash
update-grub
```

### Load VFIO modules On Boot

```bash
echo 'vfio' >> /etc/modules
echo 'vfio_iommu_type1' >> /etc/modules
echo 'vfio_pci' >> /etc/modules
echo 'vfio_virqfd' >> /etc/modules
```

Run only once. Check the file first with nano using:

```bash
nano /etc/modules
```

### Blacklist Nouveau Drivers (in favour of official ones)

```bash
echo "options kvm ignore_msrs=1" >> /etc/modprobe.d/kvm.conf
echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf
```

Again, run this only once. Check the file first with nano using:

```bash
nano /etc/modprobe.d/blacklist.conf
```

Update Kernel Changes

```bash
update-initramfs -u
```

> Reboot

### Install NVIDIA Drivers

```bash
chmod -R +x vgpu_unlock
chmod +x NVIDIA------.run
./NVIDIA------.run  --dkms
```

Note: Replace ------ with the correct driver version.

`--dkms` is mandatory for it to be loaded as a kernel module.

> Yes and Ok

