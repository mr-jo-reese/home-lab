# Install OPNsense on Proxmox

192.168.1.254

```bash
mkdir images && cd $_
wget https://mirror.ams1.nl.leaseweb.net/opnsense/releases/24.7/OPNsense-24.7-nano-amd64.img.bz2

bunzip2 OPNsense-24.7-nano-amd64.img.bz2

qemu-img convert -f raw OPNsense-24.7-nano-amd64.img -O qcow2 OPNsense-24.7-nano-amd64.qcow2

#VM Variablen
VMID="100"
OS_TYPE="l26"
CORE_COUNT=2
RAM_SIZE="2048"
HN="FW"

STORAGE="local-zfs"
FILE="/root/images/OPNsense-24.7-nano-amd64.qcow2"

qm create $VMID \
--agent 1 \
--ostype $OS_TYPE \
--name $HN \
--cores $CORE_COUNT \
--memory $RAM_SIZE \
--net0 virtio,bridge=vmbr0 \
--net1 virtio,bridge=vmbr1 \
--net2 virtio,bridge=vmbr2 \
--serial0 socket

qm disk import $VMID ${FILE} $STORAGE
qm set $VMID --scsi0 local-zfs:vm-$VMID-disk-0,cache=writeback,discard=on,ssd=1 \
--boot order=scsi0
qm disk resize $VMID scsi0 +47G
qm start $VMID

```
