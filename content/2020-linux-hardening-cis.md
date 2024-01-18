+++
title = "Linux Hardening with CIS Controls"
date  = 2020-07-08
description = "Applying CIS controls for improving the security of an Arch Linux."

[taxonomies]
tags = ["unix", "arch", "security"]

[extra]
image = "images/logos/archlinux.png"
+++

This is a direct sequence of [Installing Arch Linux](https://lopes.id/installing-arch-linux), which already includes some hardening practices.  This guide will go one step further because I am applying some [CIS](https://www.cisecurity.org/cis-benchmarks/) controls specific for Linux environments, obviously scoping and tailoring for my personal purposes.


## Security x Usability
Before starting, just a disclaimer.  Obviously many hardening techniques can be applied to improve a system's security, but typically security is inversely proportional to usability.  That said, despite many configurations can be set to improve one's system, the purpose of this system must be well understood, so these controls will not make the system unusable.

In this case, as explained in the [previous text](https://lopes.id/installing-arch-linux), I am aiming to configuring an operating system to be installed in a laptop for personal usage.  That's why I am not using the [`linux-hardened`](https://wiki.archlinux.org/index.php/Security#Kernel_hardening) kernel here for instance.  All in all, always remember that even a well-hardened system can be owned simply because technology is just another aspect of security.  To keep all systems safe, people who use them must be trained, and the processes also must be revised and in compliance with the best practices.


## Basic System's Security
This section aggregates general-purpose controls.  The first one will set the message presented before logging into the system, to avoid expose any details on the system --helps preventing footprinting.

```sh
echo "\d \t (\U) \n:\l" > /etc/issue
```

Next, the services enabled must be reviewed and unnecessary ones should be deactivated.

```sh
systemctl list-unit-files --state enabled
systemctl disable unit.service  # use this to disable services
```

Assuring the system's clock is always right and synchronized with reliable sources is essential because some protocols rely on it and the logs can become useless.

First, edit the `/etc/systemd/timesyncd.conf` uncommenting the lines starting with `NTP=` and `FallbackNTP=`.  Insert on the first line reliable sources separated by spaces --I like to use those provided by [NTP.br](https://ntp.br/).

Save and exit the editor and run the commands in the next listing to enable NTP, synchronize the clock, and enable permanently this service.

```sh
timedatectl set-ntp true
timedatectl status
timedatectl show-timesync --all
systemctl enable systemd-timesyncd.service
```


## GRUB Security
All hardening techniques become useless if one can just boot the system in rescue mode, so properly configuring the boot loader is essential.  Here, we are going to create a user and a password for every option in GRUB, except the default option to load the OS.

Create a hashed password and put it into `/etc/grub.d/40_custom`.  Since this hash is a long string and I am supposing you are in a pure text mode, the easiest way is to redirect the output of the GRUB's command to the file as shown in the next listing.

```sh
grub-mkpassword-pbkdf2 | tee -a /etc/grub.d/40_custom
```

Now open `/etc/grub.d/40_custom` in a text editor and remove the first lines created by the last command --the ones asking for passwords-- and insert the lines from the next listing.  Note that the last line must be completed with the hashed password.

```sh
set superusers="root"
password_pbkdf2 root grub.pbkdf2.sha512.10000.4E073435...
```

Remake the GRUB's configuration file --`/boot/grub/grub.cfg`-- using the next command, then open this file in a text editor.

```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

Find the menu entry for Arch Linux's default boot --`/boot/grub/grub.cfg`-- and insert the `--unrestricted` option right after the 'Arch Linux' string to avoid typing GRUB's user and password at every boot.  This line will look like `menuentry 'Arch Linux' --unrestricted --class arch...`.


## Firewall
Configure a firewall to control and log all access to the machine.  Since [ufw is already installed](https://lopes.id/installing-arch-linux) and it provides a friendly interface to iptables, we are going to use it.  The next command listing shows how to enable ufw logs, set up the default policies, start and enable the service, test if it is active, and see the service's logs.

```sh
ufw logging on

ufw default allow outgoing
ufw default deny incoming

systemctl start ufw
systemctl enable ufw
ufw enable

ufw status
journalctl -o verbose | grep -i ufw  # to see ufw logs
```

{% admonition(type="info", title="Update") %}
2021-10-26: To see the firewall logs continuously as in `tail -f`, simply run `journalctl --follow | grep -i ufw`.
{% end %}


## PAM
To avoid the temptation of creating weak passwords, [PAM](https://en.wikipedia.org/wiki/Linux_PAM) must be properly configured to implement a password policy, as well as securely control the login process.  Here we are going to implement this password policy:

- Minimum length: 14 characters.
- Required characters: Uppercase, lowercase, digits, and special characters.
- Different characters between passwords: 3 characters.
- Retries: 3 times.
- Retries before locking: 6 times.
- Time between each retry: 3 seconds.
- Time before automatic account lockout: 10 minutes.
- Storing method: SHA-512 in `/etc/shadow`.

To achieve it, open `/etc/pam.d/passwd` in a text editor, comment all lines --considering you have not changed this file yet-- and add the next lines.

```sh
password required pam_cracklib.so retry=3 minlen=14 difok=3 dcredit=-1 ucredit=-1 ocredit=-1 lcredit=-1
password required pam_unix.so     use_authtok sha512 shadow
```

{% admonition(type="info", title="Update") %}
2021-10-26: Looks like `pam_tally2.so` [was dropped in `pam` v1.4.0](https://forum.endeavouros.com/t/watch-out-pam-1-4-0-may-require-manual-intervention-pam-tally-dropped/7141) so the next step **must** be ignored (tally configuration).
{% end %}

Open the `/etc/pam.d/system-login` file and edit --or comment-- the `pam_tally2.so` line changing that for the line shown in the next listing.

```sh
auth required pam_tally2.so deny=6 unlock_time=600 onerr=fail file=/var/log/tallylog
```

Finally, edit the `/etc/pam.d/su` file to restrict the `su` access to members of the wheel group.  Uncomment or add the next line. 

```sh
auth required pam_wheel.so use_uid
```

It worth saying that the file `/etc/login.defs` overrides any settings defined by PAM, but in my case, it is pretty well configured, so there was no need to change it.  Also, remember that a time out must be set so the shell will log off when inactive.  This configuration is implemented in my [dotfiles](https://github.com/lopes/dotfiles) for Bash and Zsh.

### PAM Troubleshooting
Since PAM drastically changes the login process, any mistake can make the system useless.  I recommend testing the system after making these changes because we are going to block root access in the next steps.

To audit login errors from a given user, use the command below.  If this user is blocked, you can release it by appending a `--reset` option in the same command.

{% admonition(type="info", title="Update") %}
2021-10-06**: Since [tally was dropped from `pam`](https://forum.endeavouros.com/t/watch-out-pam-1-4-0-may-require-manual-intervention-pam-tally-dropped/7141), the command below is not present in Arch anymore.
{% end %}

```sh
pam_tally2 --user=root
```

{% admonition(type="warning", title="Warning") %}
If everything goes wrong, you will want to boot your system in rescue mode to fix the things, so be sure you know how to do it.
{% end %}


## Sudo
Since the root user will be disabled and the `su` command too, the only way to perform administrative tasks will be using Sudo, so it must be properly configured.  It is highly recommended to use the command `visudo` to perform all changes and it requires that the environmental variable `EDITOR` is configured --see my [dotfiles](https://github.com/lopes/dotfiles) to learn how to do it.

Having everything set, run the command `visudo` and start by locating the aliases section.  Create aliases like those in the next listing, be known that these commands will be executed by all users in the `wheels` groups without having to type any passwords.

```sh
Cmnd_Alias UPDATE = /usr/bin/pacman -Syu
```

Find the 'Defaults' section and add the next lines.  Here we are allowing logins from real TTYs even for remote access, so if you use SSH, remember to add the `-t` option to get in the system.

```sh
Defaults env_reset
Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
Defaults requiretty
Defaults use_pty
Defaults umask=0022
Defaults umask_override
```

Finally, a couple of lines below the definitions for the `wheels` group are set.  Add the line below to allow the execution of the defined aliases by the members of this group.

```sh
%wheel ALL=(ALL) NOPASSWD: UPDATE
```


## Update the System
We are already at the final lap and now we want to guarantee that the packages are retrieved using a secure protocol --HTTPS.  You can manually edit the file `/etc/pacman.d/mirrorlist` to achieve it or you can use [Arch's mirrorlist](https://www.archlinux.org/mirrorlist/all/https/) generator to create a list and then, edit it at your own will.

Next, you should review all installed packages and remove unnecessary ones.  Then just make sure your system is up-to-date.  To learn more about `pacman`, read [its official guide](https://wiki.archlinux.org/index.php/pacman).

```sh
pacman -Qq    # lists all installed packages
pacman -Rs    # removes the package and unused dependencies
pacman -Syu   # upgrades all packages
```


## Finishing
Now it is time to disable the root login --next command-- and reboot the system.  If you need to change this in the future, type the second command.

```sh
passwd -l root       # disable root login
sudo passwd -u root  # enable root login
```

Be responsible when using all users with Sudo powers.  Despite the fact there is no root anymore, this user must be considered the system administrator, so any malware or misuse can deeply affect the security of the OS.

Finally, remember that security is made by many aspects and this guide covered only the **technology** one.  You should be aware of taking prudent actions in the day-by-day tasks, as well as following best practices to avoid any problems.

After rebooting your system you should be more secure and ready to rock!


## Bonus: Booting Arch in Rescue Mode
Since Arch uses systemd, the equivalent to single-user mode is the rescue mode and to use it, in the GRUB menu select the appropriate kernel image --a.k.a. menu option-- and press `e`.  If you protected this menu as shown in this text --hope you did!--, you will have to type the user and password previously defined.

In the next screen, find the kernel configuration line --the one that starts with `linux /vmlinuz-linux root=...`.  Append the string in the next listing at the end of this line, then press `control-x` to boot the system.  You will still have to type the password to decrypt the disk if using [full disk encryption](https://lopes.id/installing-arch-linux) and the root password so the system will launch a shell for maintenance.

```sh
systemd.unit=rescue.target
```


## References
- [CIS](https://www.cisecurity.org/cis-benchmarks/) Distribution Independent Linux v2.0.0-07-16-2019
- [Arch Linux's systemd-timesyncd](https://wiki.archlinux.org/index.php/systemd-timesyncd)
- [Arch Linux's GRUB Tips and Tricks](https://wiki.archlinux.org/index.php/GRUB/Tips_and_tricks)
- [Arch Linux's PAM](https://wiki.archlinux.org/index.php/PAM)
- [Arch Linux's Sudo](https://wiki.archlinux.org/index.php/Sudo)
