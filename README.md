# 2420_makeup
<h1>How to Install Arch Linux after booting it from a disk image</h1>
![][1.png]
When you first boot from your disk image, you will see a CLI
<h3>Step 1: Selecting your keyboard layout</h3>
If you're using the US Keyboard layout, you can skip this step. 
This command will list out all available keyboard layouts
**ls /usr/share/kbd/keymaps/**/*.map.gz**
![](3.png)
Use this command to select your keyboard layout
**loadkeys KEYBOARD_LAYOUT**
![][4.png]

<h3>Step 2: Verify your boot mode</h3>
If this command runs without errors, then you're using UEFI. If this directory doesn't exist, then you're using BIOS or CSM. If those are not your desired boot mediums, go to your motherboard manual to change them in your BIOS
**ls /sys/firmware/efi/efivars**

<h3>Step 3: Connect to Internet</h3>
This command will verify if your network Interface are listed and enabled
**ip link**
Plug in your ethernet cable to connect to the internet. If you're using wifi, then you can use iwctl to connect to wifi
Use this command to verify that you're connected to the internet.
**ping google.com**
![][5.png]

<h3> 4: Update the system clock</h3>
Use this command to ensure that your system time is correct 
**timedatectl status**
![][6.png]

<h3>Step 5: Partition the disk</h3>
This command will list all disks
**fdisk -l**
![][7.png]
Now choose the disk you want to install Arch in and then Partiton it. Use this command to Select disk
**fdisk /dev/DISK_NAME**
![][9.png]
If you're UEFI, then create efi partition first, otherwise just create a root partition. We're only creating a root partition but you can create a home partiton and swap partiton too. 

<h3>Step 6: Formatting the partition</h3>
We will format our root partition to ext4 file system and if we created a efi partition, that too Fat32. If we also had a swap partition, we will format it to swap
This will format the single root partition to ext4
**mkfs.ext4 /dev/sda1**
![][10.png]

<h3>Step 7: Mount the root partiton</h3>
Now we will mount the root partition we just created. If we created any other partitons, we will mount them too. 
mount /dev/sda1 /mnt
![][10.png]

<h3>Step 8: Installing the kernal</h3>
This command will install essential base packages, kernal and firmware, and also a text editon VIM, which will be useful later
**pacstrap /mnt base linux linux-firmware vim**
![][12.png]
This will take some time so make a cup of coffee in the meantime.

<h3>Step 9: Configuring the System</h3>
This  command will generate fstap file
genfstab -U /mnt >> /mnt/etc/fstab

This command will change root to the new system we just installed
arch-chroot /mnt

This command will set your local timezone
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
For example, for Vancouver, change /Region/City to America/Vancouver

Then Run this to generate /etc/adjtime
hwclock --systohc

Run passwd to set root passwd
passwd

Use vim to set up localisation
vim /etc/locale/conf
Remove the hashtag infront of the lanaguage your prefer and save
then run
locale-gen

Run this command to set your hostname 
vim /etc/hostname

Install grub(on non efi system)
pacman -S grub
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg

<h3>Step 10: Creating a new user</h3>
Use this command to add a new user
useradd -m USERNAME

Then set its password
passwd USERNAME

Now give this user sudo permission
usermod -aG sudo USERNAMEexit

<h3>Reboot</h3>