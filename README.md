# archinstall
Step by step how to install Arch

#check machine ip and connect with ssh
ip addr

#change root password so you can connect with ssh
passwd

#check if it's uefi boot mode
ls /sys/firmware/efi/efivars
#if there is a lot of files you are in uefi mode

#check system clocl
timedatectl status 
#if it's incorrect 
timedatectl set-ntp true

#boot partition for UEFI 
#checking disk
lsblk
#tool for formating disk
cgdisk /dev/sda 
new > (for first sector press enter) > size in sectors = +1G > hex code = ef00 > name = boot
new > (for first sector press enter) > size in sectors = +2G > hex code = 8200 > name = swap
new > (for first sector press enter) > size in sectors (press enter for rest of disk) > hex code = 8300 > name = arch
WRITE > yes > quit

#fortmat partitions 
mkfs.fat -F32 /dev/sda1
mkswap /dev/sda2
mkfs.ext4 /dev/sda3

#mount root partitions
mount /dev/sda3 /mnt
swapon /dev/sda2
#create boot partition
mkdir -p /mnt/boot
mount /dev/sda1 /mnt/boot

#install packages
pacstrap /mnt base linux linux-firmware base-devel nano networkmanager

#generate fstab
genfstab -U /mnt >> /mnt/etc/fstab

#enter in the system 
arch-chroot /mnt

#setup timezone
ln -sf /usr/share/zoneinfo/Europe/Sofia /etc/localtime
hwclock --systohc

#edit locale
nano /etc/locale.gen
#find where says en_US.UTF-8 UTF-8 and remove # sign (row 14 at begining)
ctrl+x > y > enter
locale-gen
nano /etc/locale.conf   > LANG=en_US.UTF-8  >  ctrl+x > y > enter

#create hostnamefile and name the machine
nano /etc/hostname > (put some name for the machine) ctrl+x > y > enter

#add initframes
mkinitcpio -P

#set root password for installed machine
passwd

#make normal user
useradd -m NAME
passwd NAME
pacman -S sudo
usermod -aG wheel,audio,video,optical,storage NAME
EDITOR=nano visudo
find where says "# %wheel ALL=(ALL) NOPASSWD: ALL" and remove # infront

#install bootloader
pacman -Sy grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB

#mk grub config
grub-mkconfig -o /boot/grub/grub.cfg


--------------------------------------------------


pacman -Sy networkmanager xorg xorg-xinit lightdm lightdm-gtk-greeter fluxbox alacritty firefox git nitrogen picom xf86-video-fbdev

nano /etc/lightdm/lightdm.conf [Seat:*]
find greeter-session=lightdm-yourgreeter-greeter and uncoment it
systemctl enable lightdm
systemctl enable NetworkManager


REBOOT



