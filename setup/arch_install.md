## Install Arch Linux (minimal) :relaxed:

## Manual installation without archinstall script

### After boot from USB

-   Connect to Wifi

```bash
iwctl device list
iwctl station wlan0 scan
iwctl station wlan0 get-networks
iwctl station wlan0 connect "your wifi name"
```

-   Check internet connection

```bash
ping archlinux.org
```

### Partition the disks

-   List all the disk and partitions on your system

```bash
lsblk
```

-   Create a partition

```bash
fdisk /dev/sdX
Create a new partition: n
```

> Change sdX to your disk name or use ```cfdisk```

> An EFI system partition of 1 GB size<br>
> A SWAP partition of 4 GB size (equal to your RAM)<br>
> A root partition of 25 GB size (minimum)

-   Change the partition type

```bash
change the partition type: t

number: 1 <EFI system>
number: 19 <SWAP>
number: 83 <Linux filesystem>

write the changes: w
```

-   Format the partition

```bash
mkfs.fat -F 32 /dev/sdX1
mkswap /dev/sdX2
mkfs.ext4 /dev/sdX3
```

-   Mount the partition

```bash
mount /dev/sdX3 /mnt
mount /dev/sdX1 --mkdir /mnt/boot
swapon /dev/sdX2
```

-   Check the partitions again

```bash
lsblk
```

-   Result

```bash
/dev/sdX1 /mnt/boot
/dev/sdX2 [SWAP]
/dev/sdX3 /mnt
```

> [!CAUTION]<br>
> If error or fail, format the partition and try again.

> /dev/sdX1 EFI system partition<br>
> /dev/sdX2 SWAP partition<br>
> /dev/sdX3 root partition<br>

### Install Arch Linux

```bash
pacstrap -K /mnt base base-devel linux linux-firmware linux-headers
```

-   After installation of the base system

```bash
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```

> more packages
> ```bash
> pacman -S networkmanager grub efibootmgr nano sudo git vim neofetch bash-completion intel-ucode (or amd-ucode)
> ```
> for dual boot
> ```bash
> os-prober
> ```

> [!NOTE]<br>
> for dual boot
>
> ```bash
> echo 'GRUB_DISABLE_OS_PROBER=false' >> /etc/default/grub
> ```

```bash
mkdir /boot/efi
mount /dev/sdX1 /boot/efi

grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi

grub-mkconfig -o /boot/grub/grub.cfg
```

-   Enable NetworkManager auto start

```bash
systemctl enable NetworkManager
```

-   Set timezone

```bash
ln -sf /usr/share/zoneinfo/Asia/Ho_Chi_Minh /etc/localtime
hwclock --systohc
```

> Change Asia/Ho_Chi_Minh to your timezone

-   Set root password

```bash
passwd
```

-   Add user and set password

```bash
useradd -m -G wheel your_username
passwd your_username
```
```bash
EDITOR=nano visudo
```

> Uncomment the line<br>
> ``` %wheel ALL=(ALL) ALL ```<br>
> to allow members of the wheel group to execute any command.

-   Setup locale

```bash
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
```
```bash
echo "LANG=en_US.UTF-8" > /etc/locale.conf
locale-gen
```

-   Setup hostname

```bash
echo "arch" > /etc/hostname

nano /etc/hosts
```

> Change arch to your hostname
>
> ```bash
> 127.0.0.1	localhost
> ::1       localhost
> 127.0.1.1	arch
> ```

### Reboot

```bash
exit
umount -R /mnt
shutdown now
```

### Desktop environments

- [Desktop Environment](https://wiki.archlinux.org/title/Desktop_environment)
- Choose your favorite desktop environment :zzz:

## Done :dizzy_face:

[Arch Linux Wiki](https://wiki.archlinux.org/title/Installation_guide)
