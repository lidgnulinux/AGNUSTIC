# Install fedora 35 by bootstrap (via ubuntu distro).

## Preparation :

- dnf / yum tool, we can install it by run `apt install dnf yum4` tool.
- A repository for fedora, /etc/yum/repos.d/fedora.repo

<br>

   It contains something like this (4 lines):

<br>

      name=fedora
      baseurl=http://mirrors.bfsu.edu.cn/fedora/releases/35/Everything/x86_64/os/
      repo_gpgcheck=0
      enabled=1 

- A partition for root (/).
- Internet connection.

<br>

## Preparing partition.

- Formating partition

<br>

      $ sudo mkfs.ext4 /dev/abc1

<br>

- Mounting partition.

<br>

      $ sudo mount /dev/abc1 /mnt

<br>

## Bootstrap steps.

- Installing base system.

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

## Advance configurations.

- Copying /etc/resolv.conf file.

<br>

      $ sudo cp -r /etc/resolv.conf /mnt/etc/reolv.conf

<br>

- Chroot

<br>

      $ cd /mnt
      $ sudo mount -o bind /dev dev/
      $ sudo mount -o bind /sys sys/
      $ sudo mount -o bind /run run/
      $ sudo mount -t proc proc proc
      $ sudo chroot /mnt /bin/bash

<br>

- Updating package (optional).

<br>

      # dnf update

<br>

- Adding user and its password.

<br>

      # useradd -c USERNAME -m -g users -G wheel -s /bin/bash USERNAME
      # passwd USERNAME

<br>

- Setting root password.

<br>

      # passwd

<br>

- Setting fstab.

<br>

      # blkid > /etc/fstab
      # vim /etc/fstab

<br>

- Setting timezone.

<br>

      # timedatectl set-timezone Asia/Jakarta

<br>

- Install grub and kernel package. 

<br>

      # dnf install grub2 kernel

<br>

- Rebuild initramfs (Ahmetcan's advice)

<br>

      # dracut -Nfv --regenerate-all

<br>

- Disable selinux

<br>

      # vi /etc/selinux/config
      
      Look for line contains SELINUX=...... , change it into "disabled", without quote.

<br>

- Installing grub and updating grub.

<br>

      # grub2-install /dev/abc
      # grub2-mkconfig -o /boot/grub2/grub.cfg

<br>

- Exit chroot.

<br>

      # exit

<br>

- Unmount some filesystems we mount before.

<br>

      # cd /mnt
      # umount dev
      # umount sys
      # umount run
      # umount proc
      # cd /
      # umount /mnt

<br>

- Reboot

<br>

      # reboot

<br>

## Catatan !

- It's just a test and for have fun !
- No WM or DE installed !
- Do it from qemu or virtual box first !
- Selinux is disable so user can login.
- Installed fedora might be unstable because we use non-standard installation procedure..

<br>

## Credit.

- Ahmetcan, for the basic tutorial.
- Rahul, For disable selinux tutorial.
- LIDG (Layanan Instalasi Distro GNU/Linux).
