## Masuk ke Dalam Sistem

```bash
arch-chroot /mnt
```

## Membuat Hostname

```bash
echo stardust > etc/hostname
```

## Mengatur Waktu dan Bahasa

```bash
ln -fs /usr/share/zoneinfo/Asia/Jakarta /etc/localtime
```

```bash
hwclock --systohc
```

## Mengatur Bahasa

```bash
nvim /etc/locale.gen
```

pagarnya apus pada bagian:

```text
en_US.UTF-8 UTF-8
en_US ISO-8859-1
```

```bash
locale-gen
```

```bash
locale > /etc/locale.conf
```

```bash
nvim /etc/locale.conf
```

LANG=C.UTF-8 diganti jadi LANG=en_US.UTF-8 lalu dibagian bawahnya tambahkan LC_ALL=en_US.UTF-8

## Membuat User

```bash
useradd -m stardust (nama grup)
```

```bash
passwd stardust
```

membuat password

```bash
echo "stardust ALL=(ALL:ALL) ALL" >> /etc/sudoers.d/stardust
```

## Mengatur Parameter

```bash
mkdir /etc/cmdline.d
```

```bash
touch /etc/cmdline.d/{01-boot.conf,06-misc.conf}
```

```bash
echo "rd.luks.name=$(blkid -s UUID -o value /dev/nvme0n1p7)=stardust root=/dev/system/root" > /etc/cmdline.d/01-boot.conf
```

```bash
echo "rw" > /etc/cmdline.d/06-misc.conf
```

## Pindah ke Folder Boot

```bash
cd /boot
```

```bash
mkdir kernel efi
```

```bash
cd efi
```

```bash
mkdir linux
```

```bash
cd ..
```

```bash
mv vmlinuz-* intel-* kernel
```

```bash
ls
```

```bash
rm -fr initramfs-linux-lts.img
```

```bash
ls
```

```bash
ls efi/
```

```bash
ls kernel/
```

## Mengatur mkinitcpio

```bash
mv /etc/mkinitcpio.conf /etc/mkinitcpio.d/default.conf
```

```bash
nvim /etc/mkinitcpio.d/default.conf
```

(dibagian HOOKS tambahkan sd-vconsole lvm2 sd-encrypt setelah kms keyboard)

mengedit file konfigurasi

```bash
nvim /etc/mkinitcpio.d/linux-lts-preset
```

(EFI ganti boot)

## Menginstall Bootloader

```bash
bootctl --path=/boot install
```

namun karena error yang disebabkan bootnya belum termounting maka disolving terlebih dahulu dengan memounting bootnya lagi yaitu:

```bash
mount /dev/nvme0n1p6 /mnt/boot
```

```bash
arch-chroot /mnt
```

```bash
bootctl --path=/boot install
```

karena eror lagi maka solving dengan:

```bash
ls -l /etc/mkinitcpio.d/default.conf
```

```bash
ls -d /lib/modules/*lts*
```

```bash
ls -la /boot/efi/linux/
```

```bash
nvim /etc/mkinitcpio.d/linux-lts.preset
```

lalu pada config bagian:

```text
ALL_kver="/boot/kernel/vmlinuz-linux-lts"
```

isinya diganti menjadi:

```text
6.8.35-l-lts
```

dan begitupun dengan:

```text
ALL_kerneldest="6.8.35-l-lts"
```

isinya diganti dengan:

```text
/boot/kernel/vmlinuz-linux-lts
```

```bash
mkinitcpio -P
```

## Aktifkan Sistem

```bash
systemctl enable systemd-resolved
systemctl enable systemd-networkd
systemctl enable firewalld
```

setelah itu exit

```bash
exit
```

## Upload Asciinema

```bash
umount -R /mnt
```

lalu reboot

```bash
reboot
```
