## Install Arch Linux (minimal) :relaxed:

## Not use acrhinstall script

### After boot from USB

-   Connect to wifi

```
iwctl device list

iwctl station wlan0 connect "your wifi name" password "your wifi password"
```

-   Check internet connection

```
ping 1.1.1.1
```

-   Select an appropriate mirror

```
pacman -Syy
pacman -S reflector
reflector --latest 5 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
```

### Partition the disks

-   List all the disk and partitions on your system

```
lsblk
```

-   Create a partition

```
fdisk /dev/sdX
Create a new partition: n
```

> Change sdX to your disk name

> An EFI system partition of 1 GiB size<br>
> A SWAP partition of 4 GiB size (equal to your RAM)<br>
> A root partition of 25 GiB size (minimum)

-   Change the partition type

```
change the partition type: t

number: 1 <EFI system>
number: 19 <SWAP>

write the changes: w
```

-   Format the partition

```
mkfs.fat -F 32 /dev/sdX1
mkswap /dev/sdX2
mkfs.ext4 /dev/sdX3
```

-   Mount the partition

```
mount /dev/sdX3 /mnt
mount --mkdir /dev/sdX1 /mnt/boot
swapon /dev/sdX2
```

-   Check the partitions again

```
lsblk
```

-   Result

```
/dev/sdX1 /mnt/boot
/dev/sdX2 [SWAP]
/dev/sdX3 /mnt
```

> [!CAUTION]<br>
> If error or fail, format the partition and try again.

> /dev/sdX3 root partition<br>
> /dev/sdX1 EFI system partition<br>
> /dev/sdX2 SWAP partition

### Install Arch Linux

```
pacstrap /mnt base base-devel linux linux-firmware linux-headers networkmanager git nano grub os-prober efibootmgr dosfstools mtools vim sudo
```

> ```
> neofetch bash-completion intel-ucode (or amd-ucode)
> ```

-   After installation of the base system

```
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```

> [!TIP]<br>
> If dual boot
>
> ```
> echo 'GRUB_DISABLE_OS_PROBER=false' >> /etc/default/grub
> ```

```
mkdir /boot/efi
mount /dev/sdX1 /boot/efi

grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi

grub-mkconfig -o /boot/grub/grub.cfg
```

-   Enable NetworkManager auto start

```
systemctl enable NetworkManager
```

-   Set timezone

```
ln -sf /usr/share/zoneinfo/Asia/Taipei /etc/localtime
hwclock --systohc
```

> Change Asia/Taipei to your timezone

-   Set root password

```
passwd
```

-   Add user and set password

```
useradd -m -G wheel your_username
passwd your_username

EDITOR=nano visudo
```

> Change your_username to your username<br>
> Uncomment the line %wheel ALL=(ALL) ALL to allow members of the wheel group to execute any command.

-   Setup locale

```
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
or
sed -i 's/#en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen

echo "LANG=en_US.UTF-8" > /etc/locale.conf
locale-gen
```

-   Setup hostname

```
echo "arch" > /etc/hostname

echo -e "127.0.0.1\tlocalhost\n::1\tlocalhost\n127.0.1.1\tmyarch.localdomain\tmyarch" >> /etc/hosts
or
nano /etc/hosts
```

> Change arch to your hostname
>
> ```
> 127.0.0.1	localhost
> ::1       localhost
> 127.0.1.1	arch
> ```
 ### Reboot

```
exit
umount -R /mnt
shutdown now
```
## Done :dizzy_face:
[Arch Linux Wiki](https://wiki.archlinux.org/title/Installation_guide)