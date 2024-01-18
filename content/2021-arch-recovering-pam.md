+++
title = "Recovering Arch Linux from a PAM Issue"
date  = 2021-10-26
description = "How to recover Arch with a misconfigured PAM configuration."

[taxonomies]
tags = ["arch", "unix"]
+++

Working with PAM modules cuts both ways: While it helps to improve the system's security, it could be disastrous by making the system inaccessible even for root.  Months ago I wrote a tutorial on [how to harden Arch Linux](https://lopes.id/hardening-arch-linux/) and today I used that guide while setting up a new installation.

Since the tutorial instructed me to set up PAM's tally2 module, I followed all steps and only noticed that that module was missing when I tried to run the `pam_tally2` command.  After a Google search, I figured out that [tally2 was dropped](https://forum.endeavouros.com/t/watch-out-pam-1-4-0-may-require-manual-intervention-pam-tally-dropped/7141) and then I ignored the other steps related to it, but I forgot to remove the configuration line I inserted in `/etc/pam.d/system-login`.  That way, I followed the rest of the tutorial and after rebooting the system, I was not able to log into Arch.

{% admonition(type="note", title="Note") %}
According to [this text](https://unix.stackexchange.com/questions/557894/what-is-the-difference-between-pam-faillock-and-pam-tally2) `pam_tally` and `pam_tally2` modules were deprecated and `pam_faillock` was introduced in [Linux PAM](https://wiki.archlinux.org/title/PAM) 1.4.0.
{% end %}

Fortunately, I noticed what I did wrong and followed some steps to recover the system without having to reinstall it.  In the next few lines, I am going to describe how to do that.


## Boot from Installation Media
First, insert Arch's installation media in the machine, set it to boot from that media, and run it.  You will reach the very first screen to install Arch Linux.


## Configure Keyboard
Just like a fresh install, [configure the keyboard](https://lopes.id/installing-arch-linux/#keyboard) properly to avoid further problems.

```sh
loadkeys us-acentos
```


## Decrypt the Data Partition
Considering you are a good guy, you encrypted the data partition when installing Arch, so you must [decrypt it](https://lopes.id/installing-arch-linux/#encrypting) to access its contents.

```sh
cryptsetup luksOpen /dev/sda3 luks
```


## Mount the Partition
After decrypting the data, [mount](https://lopes.id/installing-arch-linux/#mounting) the root partition in `/mnt`.

```sh
mount /dev/mapper/vg0-root /mnt
```


## chroot
With the data available, [chroot](https://lopes.id/installing-arch-linux/#installation) to `/mnt` to fix the problem.  This step could be performed without chrooting, but I prefer to include this step to make sure everything will work as expected.

```sh
arch-chroot /mnt
```


## Maintenance
At this point, the system should be mounted, and you should be logged in as root.  Now it is time to solve the problem, and, in my case,  I just had to delete the tally2 line in the `/etc/pam.d/system-login` file.


## Exiting
After fixing the problem, just exit from the chroot environment, umount the data partition, and reboot.

```sh
exit
umount -R /mnt
reboot
```

After rebooting, the system should be accessible again (in my case it was).  Another way to accomplish this is to [boot Arch in rescue mode](https://lopes.id/hardening-arch-linux/#bonus-booting-arch-in-rescue-mode), but there is a [known bug](https://github.com/systemd/systemd/issues/7115) in systemd that prevents root login in rescue mode if the root user is locked ([which was made in the hardening process](https://lopes.id/hardening-arch-linux/#finishing)), so the method described here was the only one option left.

Note that some actions taken during the system's hardening can hamper the rescue processes, like the EFI boot options password (set along with Grub's hardening) and the full disk encryption itself.  But as long as you keep the passwords safe and accessible, everything will be fine.  Remember that security should be balanced with usability, so necessary actions to make a system more secure will eventually make it harder to use.
