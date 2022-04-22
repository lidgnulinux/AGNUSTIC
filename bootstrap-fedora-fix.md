# Install fedora 35 dengan bootstrap (melalui distro ubuntu).

## Spesifikasi mesin.

Mesin memiliki spesifikasi sebagai berikut :

- Bios legacy.
- MBR.
- Partisi tunggal.


## Persiapan :

- Tool dnf atau yum, bisa dipasang dengan perintah `apt install dnf yum4`.
- Repo untuk fedora, /etc/yum/repos.d/fedora.repo

<br>

   Isinya sebagai berikut (4 baris):

<br>

      name=fedora
      baseurl=http://mirrors.bfsu.edu.cn/fedora/releases/35/Everything/x86_64/os/
      repo_gpgcheck=0
      enabled=1 

- Satu partisi khusus.
- Koneksi internet.

<br>

## Persiapan partisi.

- Format partisi.

<br>

      $ sudo mkfs.ext4 /dev/abc1

<br>

- Mount partisi.

<br>

      $ sudo mount /dev/abc1 /mnt

<br>

## Langkah bootstrap.

- Memasang base system.

<br>

      $ sudo dnf install \
      --installroot=/mnt \
      --releasever=35 \
      --setopt=install_weak_deps=False \
      --setopt=keepcache=True \
      --assumeyes \
      --nodocs \
      systemd dnf glibc-langpack-en passwd rtkit policycoreutils \
      NetworkManager audit firewalld selinux-policy-targeted kbd zchunk sudo \
      vim systemd-udev rootfiles less iputils deltarpm sqlite lz4 iproute

<br>

## Konfigurasi lanjut.

- Menyalin file /etc/resolv.conf.

<br>

      $ sudo cp -r /etc/resolv.conf /mnt/etc/reolv.conf

<br>

- Chroot.

<br>

      $ cd /mnt
      $ sudo mount -o bind /dev dev/
      $ sudo mount -o bind /sys sys/
      $ sudo mount -o bind /run run/
      $ sudo mount -t proc proc proc
      $ sudo chroot /mnt /bin/bash

<br>

- Update paket (optional).

<br>

      # dnf update

<br>

- Menambahkan user beserta passwordnya.

<br>

      # useradd -c USERNAME -m -g users -G wheel -s /bin/bash USERNAME
      # passwd USERNAME

<br>

- Mengatur passwd root.

<br>

      # passwd

<br>

- Mengatur fstab.

<br>

      # vim /etc/fstab

<br>

- Mengatur zona waktu.

<br>

      # timedatectl set-timezone Asia/Jakarta

<br>

- Memasang paket grub dan kernel.

<br>

      # dnf install grub2 kernel

<br>

- Rebuild initramfs (rekomendasi dari Pak Ahmetcan).

<br>

      # dracut -Nfv --regenerate-all

<br>

- Disable selinux.

<br>

      # vi /etc/selinux/config
      
      Cari baris yang memuat SELINUX=...... , ganti dengan "disabled", tanpa tanda petik.

<br>

- Memasang grub dan mengupdate grub.

<br>

      # grub2-install /dev/abc
      # grub2-mkconfig -o /boot/grub2/grub.cfg

<br>

- Keluar

<br>

      # exit

<br>

- Unmount beberapa filesystem yg sebelumnya di-mount.

<br>

      # cd /mnt
      # umount dev
      # umount sys
      # umount run
      # umount proc
      # cd /
      # umount /mnt

<br>

- Reboot.

<br>

      # reboot

<br>

## Catatan !

- Ini hanya percobaan !
- Belum ada DE atau WM terpasang !
- Praktekkan terlebih dahulu di VM (qemu, vb dll).
- Selinux di-disable agar user bisa login (sebelum di-disable, user susah login / tty loop login).
- Fedora yang terpasang bisa saja berbeda dan mungkin kurang stabil, sejak pemasangan dengan cara ini tergolong tidak standar.

<br>

## Credit.

- Ahmetcan, untuk dasar tutorialnya.
- Rahul, untuk tutorial disable selinux.
- LIDG (Layanan Instalasi Distro GNU/Linux).
