---
title: "Arch KDE Installation"
date: 2021-03-07T12:59:53+05:30
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
In this guide, I will show steps to install KDE plasma desktop environment on Arch Linux.

## What is KDE?
KDE is one of the most popular desktop environments that is highly customizable while being lightweight. It is my desktop environment of choice due to it's ease of use and abundance of options. 

You can checkout their site to know more: <https://kde.org/plasma-desktop/>

## Requiremnts:
- Arch Linux base installation - If you havn't done this, you can follow my guide for the same: [Arch BTRFS Installaion](http://localhost:1313/posts/arch_installation/)
- Stable internet connection

## Installation
Installing kde on Arch is pretty straightforward. I would recommend going through this article on Arch Wiki: <https://wiki.archlinux.org/index.php/KDE>
### Step 1: Updating your system
Before we start, you must make sure your system is up to date.
```bash
sudo pacman -Syu
```
If any updates show up, install them and **reboot** your system.

### Step 2: Installing a video driver
Before we proceed, we have to make sure that a video driver is installed for installing a desktop environment. 

>**NOTE: Install only one of these** - if you have an Intel CPU but an Nvidia GPU, you have to install nvidia video card only. Installing more than one will cause issues.
```bash
## For AMD video cards
sudo pacman -S xf86-video-amdgpu
## For ATI video cards
sudo pacman -S xf86-video-ati
## For Intel video cards
sudo pacman -S xf86-video-intel
## For nVidia video cards
sudo pacman -S nvidia
```
After installing reboot your system once with the reboot command.
### Step 3: Installing xorg
Xorg  is the most popular display server used for Linux. This is very old especially as compared to the new Wayland server however is more stable and has much more support.

To install xorg simply type:
```bash
sudo pacman -S xorg
```
Press Enter twice to accept defaults and install all packages.

### Step 4: Installing Plasma Desktop
Now to install the actual plasma desktop package. I will be installing plasma using the plasma-meta package however if you want a more minimalist install, you can go for the plasma-desktop packages. Refer the Arch Wiki article shared above to know more.
>**Note:** You will need to manually install some packages to get certain features to work if you use the plasma-desktop package.

```bash
sudo pacman -S plasma-meta
```
You will be asked to choose system fonts and your default video player to install. I choose ttf-dejavu and photon-qt5-vlc [Backend package for VLC media player].

>**Note:** Default kde fonts look terrible. You will be better off picking ttf-dejavu or ttf-liberation.

### Step 5: Installing KDE applications
KDE also makes amazing applications that go well with it's desktop. We can install these by installing the kde-applications group.

```bash
sudo pacman -S kde-applications
```

I would highly recommend against installing all of the applications as you definitely won't use some of them ever. I would recommend lokking each one up and choosing for yourself which ones you want to install and which you don't. However if you're not sure, you can install the following:
```bash
5 13 14 44 48 49 51 52 53 58 106 132 142 145 153 170
```
![KDE Applications](/kde1.png)
If you ever need anything more, you can always install it later.

### Step 6: Enabling SDDM
Sddm is KDE's display manager (the package that handles logins). Sddm comes with plasma meta so we do not need to install it seperately however we do need to enable it.
```bash
sudo systemctl enable sddm
```
After doing this, reboot your system.

### Step 7: Rebooting into KDE
If all went well, you should see something like this:
![Sddm](/sddm.png)
Don't worry, you can easily change the way your login screen looks in settings and make it look much better.

After logging in, you should see this:
![KDE Desktop](/kde2.png)

Congratulations, you have finished full Arch Install!

## BONUS

### Changing login screen:
The default login screen in kde looks very bad and outdated. Luckily kde comes with breeze preinstalled which looks much better and modern.

System Settings>Workspace>Startup and Shutdown>Login Screen

You can select breeze or download another theme to your liking from the Download New SDDM Themes button
![Chaning SDDM](/sddm2.png)

### Changing theme:
Although, the default breeze theme looks pretty good, you might want the dark version or something different altogether. You can change by:

System Settings>Appreance>Global Theme

Changing the Global Theme will change everything else too. However, you can also change individual components if you so wish.

![Themes](/kde3.png)

KDE is very easy to customize if you know what you want. This is what my desktop looks like:
![Desktop](/myarch.png)
This desktop will just take 5 mins to setup. This is KDE at it's best, it let's you make a beautiful, functional desktop while being surprisingly fast and lightweight at the same time. So go and explore now that you have this setup!

That's it for this one.

Thank you!

