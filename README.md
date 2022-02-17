# Part I ~ «System installation»
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
#### Disk partitioning (interactive)
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
#### Base system installation
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
127.0.0.1	localhost
::1       localhost
127.0.1.1	harch.localdomain	harch
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
# Part II ~ «System setup»
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
#### Connect to wifi
```
$ sudo systemctl enable NetworkManager
$ reboot
$ sudo nmcli device wifi connect <SSID> password <password>
```
#### Allow pacman multilib
```
$ sudo vim /etc/pacman.conf
...
[multilib]
Include = /etc/pacman.d/mirrorlist
...
```
#### Some tools
```
$ sudo pacman -S bash-completion git
```
#### Video card packages
```
$ sudo pacman -S lib32-mesa vulkan-radeon lib32-vulkan-radeon vulkan-icd-loader lib32-vulkan-icd-loader
```
#### Aur helper
```
$ git clone https://aur.archlinux.org/paru.git
$ cd paru && makepkg -si && cd .. && rm -rf paru
```
#### Window manager
```
$ sudo pacman -S xorg-server xorg-xinit i3wm i3status dmenu
$ paru -S nerd-fonts-hack
$ echo exec i3 >> .xinitrc
```
#### Terminal emulator
```
$ sudo pacman -S rxvt-unicode urxvt-perls
```
#### Download my configs
```
$ git clone https://ZERDICORP:<token>@github.com/ZERDICORP/arch_config.git
$ cp -r arch_config/. . && rm -rf arch_config
```
#### Start WM
```
$ startx
```
#### Download my tools
```
$ git clone https://ZERDICORP:<token>@github.com/ZERDICORP/arch_bash_tools.git
$ mv arch_bash_tools/* Binary/ && rm -rf arch_bash_tools
$ cd Coding
$ git clone https://ZERDICORP:<token>@github.com/ZERDICORP/kpass__client.git
$ cd kpass__client && mkbin
```
#### Google chrome
```
$ paru -S google-chrome
```
#### Vim Plugin Manager
```
$ curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
$ vim
:PlugInstall
```
#### Sound settings
```
$ sudo pacman -S pulseaudio pulseaudio-alsa pulseaudio-jack alsa-utils
```



-  maim brightnessctl telegram-desktop tree jdk-openjdk openssh mariadb obs-studio
- paru -S spotify
- reboot
- sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
- sudo systemctl enable mariadb.service && sudo systemctl start mariadb.service
- sudo mysql_secure_installation
```
ENTER
n
Y
next all Y
```
- mysql -u root -p
```
MariaDB> CREATE USER 'zerdicorp'@'localhost' IDENTIFIED BY '<password>';
MariaDB> GRANT ALL PRIVILEGES ON mydb.* TO 'zerdicorp'@'localhost';
MariaDB> FLUSH PRIVILEGES;
MariaDB> quit
```
