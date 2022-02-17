# arch_config
- ip link set wlan0 up
- iwctl
  - statin wlan0 connect <wifi_name>
- ping google.com
- fsdisk -l
- cfdisk /dev/<disk_name>
  - delete all partions
  - new > 300M > type > EFI System
  - new > <all> > type > Linux filesystem
  - write
  - quit
- mkfs.vfat /dev/<EFI partion>
- mkfs.btrfs -f /dev/<Linux filesystem partion>
- mount /dev/<Linux filesystem partion> /mnt
- mkdir /mnt/boot && mkdir /mnt/boot/EFI
- mount /dev/<EFI partion> /mnt/boot/EFI
- pacstrap -i /mnt base base-devel linux-zen linux-zen-headers linux-firmware dosfstools btrfs-progs amd-ucode vim > [ENTER, ENTER, Y]
- genfstab -U /mnt >> /mnt/etc/fstab
- cat /mnt/etc/fstab
- arch-chroot /mnt
- ln -sf /usr/share/zoneinfo/Europe/Moscow /etc/localtime
- hwclock --systohc
- vim /etc/locale.gen (uncomment en_US.UTF-8 UTF-8 & ru_RU.UTF-8 UTF-8)
- locale-gen
- vim /etc/locale.conf
```
LANG=en_US.UTF-8
```
- vim /etc/vconsole.conf
```
KEYMAP=en
FONT=cyr-sun16
```
- vim /etc/hostname
```
harch
```
- vim /etc/hosts
```
127.0.0.1	localhost
::1       localhost
127.0.1.1	harch.localdomain	harch
```
- mkinitcpio -P
- passwd
- pacman -S grub efibootmgr dhcpcd dhclient networkamanager
- grub-install /dev/<disk>
- grub-mkconfig -o /boot/grub/grub.cfg
- exit
- umount -R /mnt
- reboot (remove USB)
- vim /etc/sudoers (uncomment `%wheel ALL=(ALL:ALL) ALL`)
- useradd -m -G wheel -s /bin/bash zerdicorp
- passwrd zerdicorp
- exit (login as zerdicorp)
- sudo su
- systemctl enable NetworkManager
- reboot
- sudo nmcli device wifi connect <SSID> password <password>
- sudo vim /etc/pacman.conf (uncomment `[multilib]` and next line)
- sudo pacman -S lib32-mesa vulkan-radeon lib32-vulkan-radeon vulkan-icd-loader lib32-vulkan-icd-loader xorg-server xorg-xinit i3wm i3status dmenu rxvt-unicode
- install paru:
  - git clone https://aur.archlinux.org/paru.git
  - cd paru
  - makepkg -si
  - cd .. && rm -rf paru
- paru -S nerd-fonts-hack > (ENTER)
- vim .xinit
```
exec i3
```
- git clone https://ZERDICORP:<token>@github.com/ZERDICORP/arch_config.git
- cp -r arch_config/. . && rm -rf arch_config
- startx
- [alt + enter]
- paru -S google-chrome > [ENTER, Y, q, Y] > enter password > [Y]
***
