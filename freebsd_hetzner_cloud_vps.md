# FreeBSD on Hetzner Cloud VPS

This guide explains how to install the [FreeBSD](https://www.freebsd.org/) on Hetzner Cloud VPS. This might also work for other VPS providers with the proper rescue system in place.

**Table of contents:**
 - [1. Rescue mode](#1-rescue-mode)
 - [2. Download and deploy FreeBSD](#2-download-and-deploy-freebsd)
 - [3. Post-installation](#3-post-installation)

## 1. Rescue mode

Log into Hetzner Cloud, switch to your VPS control panel, select Rescue -> Enable Rescue and Power Cycle. It takes up to 1 minute to boot into rescue mode. Your login details will be displayed in the control panel after you activate the console. You can also use previously defined SSH keys.

## 2. Download and deploy FreeBSD

We will use the FreeBSD VM image which includes everything we need.

Head over to [freebsd.org/where](https://www.freebsd.org/where/) and choose VM -> amd64 of the latest release.

At the time of writing this it was [14.0](https://download.freebsd.org/releases/VM-IMAGES/14.0-RELEASE/amd64/Latest/).

Download \*-amd64.raw.xz and make a binary copy into the drive.

Don't worry about the initial root parition size, it will extend itself after first boot.

```
wget https://download.freebsd.org/releases/VM-IMAGES/14.0-RELEASE/amd64/Latest/FreeBSD-14.0-RELEASE-amd64.raw.xz
wipefs -a /dev/sda
cat FreeBSD-14.0-RELEASE-amd64.raw.xz | xz -dc | dd of=/dev/sda bs=1M
reboot
```

FreeBSD should now boot and you can continue your setup via Console with [FreeBSD Handbook](https://docs.freebsd.org/en/books/handbook/).

![image](https://gist.github.com/assets/6292788/a900b8b3-d6bc-4d21-a523-f2ca69349e77)

## 3. Post-installation

 - Change root password: ```passwd```
 - Update the system: ```pkg update```
 - Install useful stuff: ```pkg install bash bash-completion bind-tools curl gnu-watch htop lsblk lsof mc mtr-nox11 nano vim wget whois```
- [Enable the SSH Server](https://docs.freebsd.org/en/books/handbook/security/#_enabling_the_ssh_server)
- Take care of [Security](https://docs.freebsd.org/en/books/handbook/security/)

---

If you found this article helpful, please consider [making a donation](https://en.wosp.org.pl/fundacja/jak-wspierac-wosp/wesprzyj-online) to a charity on my behalf. Thank you.

Enjoy your FreeBSD adventure!

[![image](https://gist.github.com/assets/6292788/f950a2a2-aa6f-4c83-a2af-c51c6f003503)](https://creativecommons.org/publicdomain/zero/1.0/)