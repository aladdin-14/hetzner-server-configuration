# Hetzner Dedicated Server with multiple drives Setup Guide 


This guide provides step-by-step instructions for setting up a new dedicated Hetzner server with multiple drives. If you've encountered issues merging these drives into a single location, especially for data storage purposes, this guide will help you configure your server correctly.

**Note:** The appearance of your drives may vary depending on your server's configuration, such as hardware RAID setup. Refer to the Hetzner Wiki Article for RAID configuration details if needed.

## Initial Drive Configuration

If your server doesn't have hardware-based RAID, you might see your drives listed individually when you log in, like this:

![Drive Picture](https://i.imgur.com/EvIBZzCl.png)

The setup process will slightly differ depending on whether you have a single HDD or multiple HDDs showing up. However, the general steps are as follows:

1. Run the `installimage` command to initiate the installation process.
2. Choose the Linux distribution you want to install.
3. Edit and save the configuration file as per your requirements.
4. Reboot the machine.

The most crucial part is editing the configuration file, as shown in the example below, when you have two or more drives and want to configure them as LVM (Logical Volume Manager) to utilize all available space:

```bash
# Multiple Drives Configuration
DRIVE1 /dev/sda
DRIVE2 /dev/sdb

# Disable Software RAID
SWRAID 0
SWRAIDLEVEL 1

BOOTLOADER grub

HOSTNAME your.chosen.hostname

# Setup LVM
PART /boot ext3 1024M
PART lvm vg0 all

LV vg0   swap   swap    swap    16G
LV vg0   root    /      ext4    all

IMAGE /root/images/Debian-1106-bullseye-amd64-base.tar.gz
```

Remember to save the configuration file (usually by pressing F10) and let the installation process complete.

## Adding Extra Drives to LVM

After completing the initial setup and rebooting into your new Debian installation, you'll notice that only the first drive is currently set up. Follow these steps to add additional drives to the LVM set:

1. Add a partition to the second drive (`sdb` in this example):
   - Run the `gdisk` command to set up partitions: `gdisk /dev/sdb`
   - Inside `gdisk`, press `o` to create a new GPT.
   - Then press `n` to create a new partition. Choose defaults for most questions but select `8e00` when asked for the Hex Code or GUID.
   - Finally, enter 'w' to write the partition.

2. Add the second drive to LVM:

```bash
pvcreate /dev/sdb1

vgextend vg0 /dev/sdb1

lvextend -l +100%FREE /dev/vg0/root

resize2fs /dev/mapper/vg0-root

# Check the size
df -h
```

Now, your additional drives are successfully added to the LVM set, and you can utilize all available space for your storage needs.

Feel free to adapt these instructions to your specific requirements and configurations. Enjoy your Hetzner dedicated server!

