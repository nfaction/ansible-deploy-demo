# Using Proxmox VE VM as test host

## Test VM (Proxmox)

Create a test VM on Proxmox VE using Ubuntu 22.04 (https://cloud-images.ubuntu.com/jammy/current/)

```
# change to iso dir
cd /var/lib/vz/template/iso

# Download the Ubuntu 22.04 Cloud Image
wget https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img

# create a new VM
qm create 123 --name ansible-test --net0 virtio,bridge=vmbr0

# import the downloaded disk to local-zfs storage
qm importdisk 123 jammy-server-cloudimg-amd64.img local-zfs

Successfully imported disk as 'unused0:local-zfs:vm-123-disk-0'

# finally attach the new disk to the VM as scsi drive
qm set 123 --scsihw virtio-scsi-pci --scsi0 local-zfs:vm-123-disk-0

# Set VM params
qm set 123 --cores 4
qm set 123 --memory 8192
qm resize 123 scsi0 +62G

qm set 123 --ide2 local-zfs:cloudinit
qm set 123 --boot c --bootdisk scsi0
qm set 123 --serial0 socket --vga serial0
qm set 123 --ciuser ubuntu
qm set 123 --cipassword ansible-secret
qm set 123 --sshkey ~/.ssh/id_rsa.pub
qm set 123 --ipconfig0 ip=192.168.1.123/24
qm start 123

# Login to host
ssh ubuntu@192.168.1.123

# Add Ansible deploy host ssh public key
vim ~/.ssh/authorized_keys

# REMOVE
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIO8b2H1bBkkUwgPgiNLHtUf0JOIPdA042Ug0hU/JdgIm dell-work-ws

# Validate network settings
vim /etc/netplan/50-cloud-init.yaml

# Shutdown

# Remove Cloud-init
qm unlink 123 --idlist ide2
qm set 123 --serial0 socket --vga std

qm set 123 --onboot 1

# Start VM
qm start 123

# Snapshot base install
qm snapshot 123 base-install
```

## Test Ansible

```
$ ansible all -m ping -i 192.168.1.123, -u ubuntu

192.168.1.123 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```