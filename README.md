# Arch installation note :smoking:
#### Take a deep breath and go f!ck yourself.
## Table of contents :scroll:

- [Part I ~ «System installation» :hammer:](#part-i--system-installation-hammer)
    - [Connect to wifi](#connect-to-wifi)
    - [Disk partitioning](#disk-partitioning)
    - [Make filesystems](#make-filesystems)
    - [Mounting](#mounting)
    - [Base system setup](#base-system-setup)
    - [Partition config generation](#partition-config-generation)
    - [Chroot](#chroot)
    - [Timezone](#timezone)
    - [Localization](#localization)
    - [Network settings](#network-settings)
    - [Initramfs](#initramfs)
    - [Root password](#root-password)
    - [Bootloader](#bootloader)
    - [Network utilities](#network-utilities)
    - [End of installation](#end-of-installation)

- [Part II ~ «System setup» :wrench:](#part-ii--system-setup-wrench)
    - [New user](#new-user)
    - [Allow pacman multilib](#allow-pacman-multilib)
    - [Connect to wifi](#connect-to-wifi)
    - [Aur helper](#aur-helper)
    - [Video acceleration packages](#video-acceleration-packages)
    - [Toolbox](#toolbox)
    - [My configs](#my-configs)
    - [Window manager](#window-manager)
    - [My tools](#my-tools)
    - [Password storage utility](#password-storage-utility)
    - [Tool to open a new terminal in the current directory](#tool-to-open-a-new-terminal-in-the-current-directory)
    - [Vim Plugin Manager](#vim-plugin-manager)
    - [MySQL](#mysql)
    - [Sound](#sound)
    - [End of setup](#end-of-setup)

## Part I ~ «System installation» :hammer:
#### Connect to wifi
```
$ ip a
$ ip link set <device> up
$ iwctl
iwctl> station <device> scan
iwctl> station <device> get-networks
iwctl> station <device> connect <SSID>
iwctl> exit
$ ping google.com
```
#### Disk partitioning
```
$ fsdisk -l
$ cfdisk /dev/<disk>
New ~ 300M; Type ~ EFI System;
New ~ <all other>; Type ~ Linux filesystem;
Write
Quit
```
#### Make filesystems
```
$ mkfs.vfat /dev/<EFI_partion>
$ mkfs.btrfs -f /dev/<Linux_filesystem_partion>
```
#### Mounting
```
$ mount /dev/<Linux_filesystem_partion> /mnt
$ mkdir -p /mnt/boot/EFI
$ mount /dev/<EFI_partion> /mnt/boot/EFI
```
#### Base system setup
```
$ pacstrap -i /mnt base base-devel linux-zen linux-zen-headers linux-firmware dosfstools btrfs-progs amd-ucode vim
```
#### Partition config generation
```
$ genfstab -U /mnt >> /mnt/etc/fstab
$ cat /mnt/etc/fstab
```
#### Chroot
```
$ arch-chroot /mnt
```
#### Timezone
```
$ ln -sf /usr/share/zoneinfo/Europe/Moscow /etc/localtime
$ hwclock --systohc
```
#### Localization
```
$ vim /etc/locale.gen
...
en_US.UTF-8 UTF-8
...
ru_RU.UTF-8 UTF-8
...
$ locale-gen
$ vim /etc/locale.conf
LANG=en_US.UTF-8
$ vim /etc/vconsole.conf
KEYMAP=en
FONT=cyr-sun16
```
#### Network settings
```
$ vim /etc/hostname
harch
$ vim /etc/hosts
127.0.0.1 localhost
::1       localhost
127.0.0.1 harch.localdomain harch
```
#### Initramfs
```
$ mkinitcpio -P
```
#### Root password
```
$ passwd
Qwerty123
```
#### Bootloader
```
$ pacman -S grub efibootmgr
$ grub-install /dev/<disk>
$ grub-mkconfig -o /boot/grub/grub.cfg
```
#### Network utilities
```
$ pacman -S dhcpcd dhclient networkamanager
```
#### End of installation
```
$ exit
$ umount -R /mnt
$ reboot
```
## Part II ~ «System setup» :wrench:
#### New user
```
$ vim /etc/sudoers
...
%wheel ALL=(ALL:ALL) ALL
...
$ useradd -m -G wheel -s /bin/bash zerdicorp
$ passwrd zerdicorp
$ exit
```
#### Allow pacman multilib
```
$ sudo vim /etc/pacman.conf
...
[multilib]
Include = /etc/pacman.d/mirrorlist
...
```
#### Connect to wifi
```
$ sudo systemctl enable/start NetworkManager
$ sudo nmcli device wifi connect <SSID> password Qwerty123
```
#### Git
```
$ sudo pacman -Syu git
```
#### Aur helper
```
$ git clone https://aur.archlinux.org/paru.git
$ cd paru && makepkg -si && cd .. && rm -rf paru
```
#### Video acceleration packages
```
$ sudo pacman -S lib32-mesa vulkan-radeon lib32-vulkan-radeon vulkan-icd-loader lib32-vulkan-icd-loader
```
#### Toolbox
```
$ sudo pacman -S bash-completion xclip brightnessctl zip unzip openssh tree jdk-openjdk obs-studio telegram-desktop rxvt-unicode urxvt-perls
$ paru -S maim spotify google-chrome
```
#### My configs
```
$ git clone https://github.com/ZERDICORP/arch_config.git
$ cp -r arch_config/. . && rm -rf arch_config
```
#### Window manager
```
$ sudo pacman -S xorg-server xorg-xinit i3wm i3status dmenu
$ paru -S nerd-fonts-hack
$ echo exec i3 >> .xinitrc
$ startx
```
#### My tools
```
$ git clone https://github.com/ZERDICORP/arch_bash_tools.git
$ mv arch_bash_tools/* Binary/ && rm -rf arch_bash_tools
```
#### Password storage utility
```
$ cd Coding && git clone https://github.com/ZERDICORP/kpass__client.git
$ cd ~/Coding/kpass__client/src && ./build && cd ../build && ./mkbin
```
#### Tool to open a new terminal in the current directory
```
$ cd Downloads && curl -LO https://github.com/schischi/xcwd/archive/master.zip && unzip master.zip
$ cd xcwd-master && make && sudo make install
$ cd .. && rm -rf xcwd-master master.zip
```
#### Vim Plugin Manager
```
$ curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
$ vim
:PlugInstall
```
#### MySQL
```
$ sudo pacman -S mariadb
$ sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
$ sudo systemctl enable/start mariadb.service
$ sudo mysql_secure_installation
$ mysql -u root -p
MariaDB> CREATE USER 'zerdicorp'@'localhost' IDENTIFIED BY 'Qwerty123';
MariaDB> GRANT ALL PRIVILEGES ON mydb.* TO 'zerdicorp'@'localhost';
MariaDB> FLUSH PRIVILEGES;
MariaDB> quit;
```
#### Sound
```
$ sudo pacman -S pulseaudio pulseaudio-alsa pulseaudio-jack alsa-utils
$ alsamixer
<F5>
check volume 
```
#### End of setup
```
reboot
```
