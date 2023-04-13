---
title: "Arch Linux with BTRFS Installation (Base)"
date: 2021-03-05T11:35:04+05:30
author: "NishantN"
env: 'production'
draft: false
showToc: true
disableHLJS: false
ShowShareButtons: false
ShowBreadCrumbs: false
ShowPostNavLinks: True
tags: ['linux']
---
## Introduction
In this guide, I will show steps to install Arch Linux with the BTRFS file system with several different subvolumes.
## What is Arch Linux?
Arch Linux is a rolling release linux distribution for x86-64 computers. It is widely popular due to it's ease of use and simplicity. 
You can read more about Arch Linux here : <https://wiki.archlinux.org/index.php/Arch_Linux>

## Why Btrfs?
Btrfs is a modern copy on write (CoW) filesystem for Linux aimed at implementing advanced features while also focusing on fault tolerance, repair and easy administration. It's biggest boon is enabling users to take system snapshots that do not take time to create or restore and barely take up any space on your system.

Since Arch is soo bleeding edge, it is possible that once in a while, due to an update your system breaks. Whenever such faults happen, you can simply roll back on the last snapshot in seconds if you have a BTRFs file system in place.

## Requirements
- x86_64 (64 bit) machine
- Minimum 512 MB of RAM (2 GB recommended)
- 2 GB disk space for base install (15-20 GB recommended for full install)
- An active internet connection
- VirtualBox installed or USB drive with least 2 GB storege

## Installation Process:
Arch Wiki has a great article on installing Arch Linux that you can refer while following this guide: <https://wiki.archlinux.org/index.php/installation_guide>
### Step 1: Download Arch Linux
Download the latest Arch Linux ISO from <https://archlinux.org/download/>

### Step 2: Prepare Installation Medium

#### Installing on a Virtual Machine
Follow this guide for setting up VirtualBox for Arch Linux installation: [VirtualBox Setup](https://nishantn-blog.netlify.app/posts/vm_setup/)


#### Dual booting your device
Arch should not be your first linux distro on dual boot. Hence if you are dual booting Arch, it is assuming you have dual booted another linux distro before and have everything before. This is not covered in this guide.
However the basic steps are:
1. Use [etcher](https://www.balena.io/etcher/) to burn your USB.
2. Disable Fast Startup.
3. Disable Secure Boot from BIOS.
4. Shrinking the drive where you plan to install Arch Linux with Windows Partition Manager.
5. Boot Arch installer from the Live USB created in step 1.

You can refer this for more details: <https://wiki.archlinux.org/index.php/Dual_boot_with_Windows#Windows_UEFI_vs_BIOS_limitations>

>**Note**: I highly recommend creation of a **seperate boot partition** for any linux install and leaving the Windows boot partition alone. This was if any problem occures, you can safely format the linux partition without harming Windows in any way.

### Step 3: Setting keymap (Optional)

Default keymap is US which will fit most keyboards. However if you have a different keymap you can set it using:
```bash
loadkeys [keymap]
```
(Replace [keymap] with your specific keymap)

If you are not sure which keymap you have, you can list all keymaps with:
```shell
localectl list-ketmaps
```
### Step 4: Checking internet connection
If you are using a virtual machine, make sure the base OS has internet access.

If you're installing it on your device and do not have ethernet, you can easily get wifi on your Arch install with USB tethering on your phone. Just connect your phone to your pc and enable USB Tethering in settings and you're good to go.

>**Note:** Make sure your phone us connected to WiFi and not mobile data if using USB Tethering

You can ping google to check if you're connected to the internet:
```bash
ping google.com -c 2
```
It should show something like this:
![Ping Google](/ping.png)

### Step 5: Set time
```bash
timedatectl set-ntp true
```
### Step 6: Partitioning your drive
Arch Wiki recommends fdisk to partiton and so we will stick to that.

Use this command to look at your file structure and find the name of the partition where you want to install Arch Linux on:
```bash
fdisk -l
```
![fdisk -l](/fdisk.png)
In my case, I want to install Arch on this 10GB disk called **sda**.



>**Note**: Moving forward replace sda with your disk's name for any command I use

sda is a disk whereas sda0 would be a partition on the sda disk. Since sda is not partitioned, it does not show any partitions. However, if you are dual booting on the same disk as where another OS is installed, you will have other partitions present.

Now for partitioning the disk. Type this command to select the drive to partition.
```bash
fdisk /dev/sda
```
You can type m to get a list of availabe commands:
![fdisk -l](/fdisk1.png)

For installing Arch with btrfs, we need to make 2 minimum partitions 
- /boot/efi partition (300MB size)
- /(root) partition

However another essential partiton is the /swap partition.

Swap partiton is used as additional RAM if your system runs out of RAM. It is also essential if you want to hibernate or suspend your OS. It is recommended that you set your swap storage as twice your RAM (eg. if RAM is 4GB, swap should be 8GB). However, you will almost never need more than 8GB of swap.

**Creating the Partitions**
As seen from help, we press n for creating new partitions. However before that if your partition is empty, we need to create a partition table 
>**Warning:** Type g for gpt partition table only if the drive that you are installing on is empty. Skip that step if the drive is already partitioned.

![Creating partitions](/fdisk2.png)
1,3,5 - Press enter for default values

2 - Making partition 1 with 300 MB space (for /boot partition)

4 - Making partition 2 with 1 GB space (/swap). Here, replace 1 with your required amount of swap (RAM*2).

5 - Making partition 3 with remaining space (/(root) partition)

**Changing partition type and writing changes**

As you might have noticed, the partiton type for all is linux filesystem by default. However, only /(root) partition is a linux filesystem. Hence, we need to change the filesystem type of the remaining two.

We need EFI filesystem for the /boot partition and Linux Swap for /swap partition.

You can enter l to get the list of partition types:
![Listing partitions](/fdisk3.png)
As we can see id for EFI is 1 and id for Linux Swap is 19.

Press q to quit the list.

Now to change the partitions:
![Changing filetype of partitions](/fdisk4.png)
1 - Selecting the first (/boot) partition

2 - Chaning filetype to EFI system (id - 1)

3 - Selecting the second (/swap) partition

4 - Changing filetype for Linux Swap (id - 19)

5 - Writing all changes to the disk. This will save partition changes to the disk.

**Vierifying Changes**
Now enter this command to view the changes made
```bash
fdisk -l
```
![Viewing changes](/fdisk5.png)
As can be seen, the partitions were correctly made.
### Step 7: Creating Filesystems
Now we must format the partitons with the respective file systems.

We need FAT32 file system for /boot:
```bash
mkfs.fat -F32 /dev/sda1
```
For /swap partition, we need to make the partition and activate swap so:
```bash
mkswap /dev/sda2
swapon /dev/sda2
```
For /(root), we need to make with the btrfs file system:
```bash
mkfs.btrfs /dev/sda3
```
![Making the filesystems](/make.png)

### Step 8: Mounting the partitions and subvolumes
Now we must mount the partitions that we just created (except swap as it is not used to store static files).
```bash
mount /dev/sda3 /mnt
```
Now that we have mounted the root subvolume, we must create subvolumes for btrfs.

We create subvolumes to better organize our data and to exclude them from btrfs snapshots. Also, if you're using multiple disks for a single OS (eg. Windows C: and D: drives are on different disks), subvolumes enable you to store even system files on another directory. On my personal setup, I have the @var and @tmp subvolumes on my HDD so as to save space on my SSD where Arch is installed.
```bash
btrfs su cr /mnt/@
btrfs su cr /mnt/@home
btrfs su cr /mnt/@var
btrfs su cr /mnt/@opt
btrfs su cr /mnt/@tmp
btrfs su cr /mnt/@.snapshots
umount /mnt
```
These subvolumes are mainly named after system directories which have specific functions:
- @ - This is the main root subvolume on top of which all subvolumes will be mounted.
- @home - This is the home directory. This consists of most of your data including Desktop and Downloads.
- @var - Contains logs, temp. files, caches, games, etc. 
- @opt - Contains third party products
- @temp - Contains certain temperory files and caches
- @.snapshots - Directory to store snapshots for snapper (Can exclude this if you plan on using Timeshift)

Now to mount these partitions:
```bash
mount -o noatime,commit=120,compress=zstd,space_cache,subvol=@ /dev/sda3 /mnt
# You need to manually create folder to mount the other subvolumes at
mkdir /mnt/{boot,home,var,opt,tmp,.snapshots}

mount -o noatime,commit=120,compress=zstd,space_cache,subvol=@home /dev/sda3 /mnt/home

mount -o noatime,commit=120,compress=zstd,space_cache,subvol=@opt /dev/sda3 /mnt/opt

mount -o noatime,commit=120,compress=zstd,space_cache,subvol=@tmp /dev/sda3 /mnt/tmp

mount -o noatime,commit=120,compress=zstd,space_cache,subvol=@.snapshots /dev/sda3 /mnt/.snapshots

mount -o subvol=@var /dev/sda3 /mnt/var
# Mounting the boot partition at /boot folder
mount /dev/sda1 /mnt/boot
```
Btrfs options:
- noatime - No access time. Improves system performace by not writing time when thefile was accessed.
- commit - Peridoic interval (in sec) in which data is synchronized to permanent storage.
- compress - Choosing the algorithm for compress. I have set zstd as it has good compression level and speed.
- space_cache - Enables kernel to know where block of free space is on a disk to enable it to write data immediately after file creation.
- subvol - Choosing the subvol to mount.

You can read more about btrfs mount options here: <https://btrfs.wiki.kernel.org/index.php/Manpage/btrfs(5)>

Verify that you have mounted everything correctly:
```bash
lsblk
```
![lsblk](/lsblk1.png)
The mountpoints show the last subvolume that you mounted.

### Step 9: Installing the base system

For intel CPUs:
```bash
pacstrap /mnt base linux linux-firmware nano intel-ucode btrfs-progs
```
For AMD CPUs:
```bash
pacstrap /mnt base linux linux-firmware nano amd-ucode btrfs-progs
```
For VMs:
```bash
pacstrap /mnt base linux linux-firmware nano btrfs-progs
```
Type 'y' when asked for confirmation.

pacstrap will install the packages mentioned on the newly made root partition.
Packages installed:
- base - Base linux system
- linux - Latest linux kernal and modules(You can replace with with linux-lts if you want a more stable kernel)
- linux-firmware - Firmware files for linux (You can skip this in a vm)
- nano - A simple terminal based text editor
- intel-ucode - Microcode update files for Intel CPUs
- amd-ucode - Microcode update image for AMD CPUs
- btrfs-progs - Btrfs filesystem utilities

Depending on your internet speed, this step might take time. You should see something like this when installation is done:
![pacstrap completion](/pacstrap.png)
### Step 10: Generate fstab
After installation of all packages is done, we need to now generate the fstab. The fstab file is used to define how disk partitions, various other block devices, or remote filesystems should be mounted into the filesystem. Generate it using:
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```
Verify fstab entries by: 
```bash
cat /mnt/etc/fstab
```
It should look something like this:
![fstab](/fstab.png)

### Step 11: Chroot into install
Now you must enter your Arch install to set it up:
```bash
arch-chroot /mnt
```

### Step 12: Seting timezone
You set timezone using:
```bash
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
```
Replace region and city with your own timezone.
In my case it will be:
```bash
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
```
You can list all timezones with:
```bash 
timedatectl list-timezones
```
Press 'q' to quit list

Now to sync hardware and system clock:
```bash 
hwclock --systohc
```
### Step 13: Setting System Locale
You need to manually edit a file for this:
```bash
nano /etc/locale.gen
```
You need to scroll down and uncomment the language you want. For me, since I want English US, I will scroll down and uncomment that:
![Uncommenting en_US](/locale.png)
After you uncomment, press Ctrl+O and then Enter to save and Ctrl+X to exit.

Now generate locales:
```bash
locale-gen
```
Now we set locale in locale.conf file:
```bash
echo LANG=en_US.UTF-8 >> /etc/locale.conf
```
If you choose a different language, replace en_US.UTF-8 with your language.

### Step 14: Setting Keymap (Only if you did Step 3)
If you changed your keymap in step 3, you need to add it here also:
```bash
echo KEYMAP=[keymap] >> /etc/vconsole.conf
```
Replace [keymap] with your specific keymap.

### Step 15: Network Configuration
We now need to set our Hostname
```bash
echo Arch-VM >> /etc/hostname
```
Replace Arch-VM with whatever hostname you wish to set.

Now for the hostfiles:
```bash
nano /etc/hosts
```
Arch Wiki states the format for this:
```code
127.0.0.1	localhost
::1		localhost
127.0.1.1	myhostname.localdomain	myhostname
```
So in my case, I will add:
![Adding hosts](/hostname.png)
After you add, press Ctrl+O and then Enter to save and Ctrl+X to exit.

### Step 16: Setting password for root user
```bash
passwd
```
Enter your password twice to set root password.
>**Note**: In linux, visual feedback for passwords is disabled for better security.

### Step 17: Installing remaining essential packages
```bash
pacman -S grub grub-btrfs efibootmgr base-devel linux-headers networkmanager network-manager-applet wpa_supplicant dialog os-prober mtools dosfstools reflector git
```
These are some basic sets of packages you will need if you plan to use Arch in the long run. I would recommend that you google all packages to understand what they do.

Additional things you can add:

| Package Name      |        Use         |
|-------------------|--------------------|        
|bluez & bluez-utils| Bluetooth support  | 
|       cups        | Printing support   |
| xdg-utils & xdg-user-dirs| Better integration with desktop environments|

After entering the command, press Enter to select all of the base-devel packages to install.

Then wait for the installation to finish.

### Step 18: Adding btrfs module to mkinitcpio
```bash
nano /etc/mkinitcpio.conf
```
Add btrfs in MODULES=()
![Adding btrfs module](/mkinitcpio.png)
Save and exit nano.

Now to recreate the image:
```bash
mkinitcpio -p linux
```
Replace linux with linux-lts if you installed the lts kernel

### Step 19: Installing GRUB
Installing grub:
```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id = Arch
```
Now to generate the configuration file:
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```
### Step 20: Creating a User
Adding a user:
```bash
useradd -mG wheel nishantn
```
Above command adds a user with name nishantn and gives it access to wheel group (for sudo privilages). Replace nishantn with whatever name you want.

Giving a password to the user:
```bash
passwd nishantn
```
Enter password twice

Now to give usersfrom the wheel group full sudo access:
```bash
EDITOR=nano visudo
```
Uncomment the line which says **%wheel ALL=(ALL) ALL**
![Adding btrfs module](/visudo.png)
Save and exit nano

### Step 21: Enabling services
```bash
systemctl enable NetworkManager
## If you installed bluez
systemctl enable bluetooth
## If you installed cups
systemctl enable org.cups.cupsd
```
### Step 22: Restarting into Arch
```bash
## Exiting the installation
exit
## Unmounting all drives
umount -l /mnt
## If you're installing Arch on VM
shutdown now
## If you're dual booting/installing on a device
reboot
```
**Deleting Arch iso (VM users only)**

After shutting down, go to Storage settings of your VM, select the iso file and click on remove selected.
![Remove iso](/remove_iso.png)
After restarting and logging in, it should look something like this:
![After Login](/login.png)

## After Install
Congratulations you have installed Arch Linux successfully! You would still need to go ahead and install a desktop environment or window manager on top if you want but the hard part is over. 
After completing the base install, you are now 
eligible to make an account on the prestigious Arch Linux website which contains the Wiki, Forums, etc.

Refer this to see post install guide for Arch: <https://wiki.archlinux.org/index.php/General_recommendations>

Thank you!