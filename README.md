# Auto-Mount Memories on Proxmox(8) or any Debian(12) based system
This is a set of auto-mount script & rules tested on Proxmox 8. It will  mount every partition (ext4/ntfs/fat) found on dynamically/usb attached memories (sticks, external HDDs, sd-cards...) to /mnt/auto/....

## Why?
- Let's assume, you just want to transfer some files offline to/from a usb attached memory, you would need to execute the same (rather simple) commands every time you attach the disk again. This is annoying!
- As long as you use the same partition label, you can statically create a directory storage in the pve GUI, and use it as backup location f.i.. As soon the memory containing the partition is attached, it can be used by PVE for backups.
- I found no final solution for this task (especially for pve 8 on debian 12). Other found solutions are not mantained anymore. I did not even tried them because they were made for pve7
- That's why I wrote my own solution after some try and fails.
## Important notes
- systemd-udev-settle is deprecated and it breaks the whole booting procedure. Even Networking is stopped at boot after some simple udev rules are added.
- This script will override two pve default services (if found), which are unnecessarily using systemd-udev-settle: zfs-import-cache and zfs-import-scan. The small change will take out the dependency for systemd-udev-settle. **But there is a catch:**
- **!!** If this default unit files will change in future updates, these overrides will not be aware of that. I saw no other option to take out the broken dependency. If anyone knows a better option, feel free to suggest! But there is good news also:
- The install script will only apply the overrides, if a dependency to systemd-udev-settle will be found. If they fix this in the future, the override will not be applied anymore (at install!).
- **!!** This automation was only **tested on Proxmox 8 with LVM storage** (no ZFS). If you are using zfs, use it at your own risk! Any feedback is appreciated.
- The mount folder name will be  the label of the partition, by default. If no label can be determined, the name of the device (e.g sdc2) will be used. If a directory with the same name exists already, thae the folder will get the device name as suffix.

Example:
Partition /dev/sdb1
Partition Label: myUsbData
==> Mount will be found at /mnt/auto/myUsbData
- This automation will **NOT** create a new storage in PVE. For this you have to go in the pve gui:
  - Datacenter->Storage->Add->Directory
  - Specify any ID you want (I usually specify the partition label here but it doesn't matter)
  - Directory: the automatically created mountpoint, e.g. /mnt/auto/myUsbData
  - Content: choose also VZDUmp if you want to save backups on it.

# Install
- Clone this repo to your machine. Go inside the folder.
- run ./installAutoMount.sh
- Test: Plug in a usb memory and check the /mnt/auto folder for further subdirectories. /mnt/auto will be automatically created as soon some partition can be mounted there.
