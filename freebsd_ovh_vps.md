# FreeBSD installation on OVH VPS

Source: https://gist.github.com/c0m4r/0401f6b53d35b0c34a4f51eef68af377#file-freebsd_ovh_vps-md

This guide explains how to install the [FreeBSD](https://www.freebsd.org/) on OVH VPS. This might also work for other VPS providers with the proper rescue system in place.

Inspired by https://www.klajnszmit.net/unix-bsd-linux/openbsd-on-ovh-vps

**Table of contents:**
 - [1. Rescue mode](#1-rescue-mode)
 - [2. Disk preparation](#2-disk-preparation)
 - [3. Download and deploy FreeBSD](#3-download-and-deploy-freebsd)
 - [4. Post-installation](#4-post-installation)

## 1. Rescue mode

Log into OVHcloud, switch to your VPS control panel, select Boot -> Restart into Rescue Mode. It takes up to 3 minutes to boot into rescue mode. You can read the rescue root account password from KVM (Console) or from the link sent via email.

Let's copy our SSH Key into the rescue environment first for convenience and log in:

```bash
if [ ! -e ~/.ssh/id_ed25519 ] ; then ssh-keygen -t ed25519 ; fi
ssh-copy-id root@X.X.X.X
ssh root@X.X.X.X
```

## 2. Disk preparation

Check available drives:

```bash
fdisk -l
```

> [RESCUE] root@vps-XXXXXXXX:~ $ fdisk -l\
> Disk /dev/sda: 2.93 GiB, 3145728000 bytes, 6144000 sectors\
> Disk model: QEMU HARDDISK   \
> Units: sectors of 1 * 512 = 512 bytes\
> Sector size (logical/physical): 512 bytes / 512 bytes\
> I/O size (minimum/optimal): 512 bytes / 512 bytes\
> Disklabel type: dos\
> Disk identifier: 0x9fdc479e
> 
> Device     Boot Start     End Sectors  Size Id Type\
> /dev/sda1  *     2048 6141951 6139904  2.9G 83 Linux
> 
> 
> Disk /dev/sdb: 40 GiB, 42949672960 bytes, 83886080 sectors\
> Disk model: QEMU HARDDISK   \
> Units: sectors of 1 * 512 = 512 bytes\
> Sector size (logical/physical): 512 bytes / 512 bytes\
> I/O size (minimum/optimal): 512 bytes / 512 bytes\
> Disklabel type: dos\
> Disk identifier: 0xbb326e04
> 
> Device     Boot Start      End  Sectors Size Id Type\
> /dev/sdb1  *     2048 83886079 83884032  40G 83 Linux

We are interested in /dev/sdb, which is our VPS's drive. We need to wipe what's left on that drive and exit back to our local machine:

```bash
wipefs -a /dev/sdb
exit
```

## 3. Download and deploy FreeBSD

We will use the FreeBSD VM image which includes everything we need.

Head over to [freebsd.org/where](https://www.freebsd.org/where/) and choose VM -> amd64 of the latest release.

At the time of writing this it was [14.0](https://download.freebsd.org/releases/VM-IMAGES/14.0-RELEASE/amd64/Latest/).

Download \*-amd64.raw.xz
  
```bash
wget https://download.freebsd.org/releases/VM-IMAGES/14.0-RELEASE/amd64/Latest/FreeBSD-14.0-RELEASE-amd64.raw.xz
```
  
And then transfer it over ssh:

```bash
cat FreeBSD-14.0-RELEASE-amd64.raw.xz | ssh root@X.X.X.X "xz -dc | dd of=/dev/sdb bs=1M"
```

Now make sure that everything went as expected:

```bash
ssh root@X.X.X.X
fdisk -l /dev/sdb
```

You should see something like this:

> Disk /dev/sdb: 40 GiB, 42949672960 bytes, 83886080 sectors\
> Disk model: QEMU HARDDISK   \
> Units: sectors of 1 * 512 = 512 bytes\
> Sector size (logical/physical): 512 bytes / 512 bytes\
> I/O size (minimum/optimal): 512 bytes / 512 bytes\
> Disklabel type: gpt\
> Disk identifier: 085DD663-7FB6-11EE-A7BA-002590EC5BF2
> 
> Device          Start      End  Sectors  Size Type\
> /dev/sdb1          34      155      122   61K FreeBSD boot\
> /dev/sdb2         156    66739    66584 32.5M EFI System\
> /dev/sdb3       66740  2163891  2097152    1G FreeBSD swap\
> /dev/sdb4     2163892 12649651 10485760    5G FreeBSD UFS

Don't worry about the /dev/sdb4 size, it will extend itself after first boot.

Now restart your VPS via the OVH control panel ($ reboot won't work).

FreeBSD should now boot and you can continue your setup via KVM (Console) with [FreeBSD Handbook](https://docs.freebsd.org/en/books/handbook/).

![image](https://gist.github.com/assets/6292788/a900b8b3-d6bc-4d21-a523-f2ca69349e77)

## 4. Post-installation

 - Change root password: ```passwd```
 - Update the system: ```pkg update```
 - Install useful stuff: ```pkg install bash bash-completion bind-tools curl gnu-watch htop lsblk lsof mc mtr-nox11 nano vim wget whois```
- [Enable the SSH Server](https://docs.freebsd.org/en/books/handbook/security/#_enabling_the_ssh_server)
- Take care of [Security](https://docs.freebsd.org/en/books/handbook/security/)

---

If you found this article helpful, please consider [making donation](https://en.wosp.org.pl/fundacja/jak-wspierac-wosp/wesprzyj-online) to a charity on my behalf. Thank you.

Enjoy your FreeBSD adventure!

[![image](https://gist.github.com/assets/6292788/f950a2a2-aa6f-4c83-a2af-c51c6f003503)](https://creativecommons.org/publicdomain/zero/1.0/)
