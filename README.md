# Proxmox GPU Sharing Passthrough

Share one GPU with any number of VMs created using Proxmox

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
apt -y install python3 python3-pip git build-essential pve-headers dkms jq

pip3 install frida
```
