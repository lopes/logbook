+++
title = "Creating a Hardened Testing Environment"
date  = 2021-11-04
description = "Install and harden Arch Linux to create a safer OS for network tests."

[taxonomies]
tags = ["arch", "unix", "security", "networking"]
+++

In my job, we needed to perform some networking tests in an unsafe network segment, so I decided to make a machine for that purpose, granting that the risks were mitigated by hardening the operating system.  In this post, I describe the steps to create this environment.


## Installation and Hardening
The first thing was to install Arch and it is documented [in this post](https://lopes.id/installing-arch-linux/), including full disk encryption.  After installing Arch, I [put my dotfiles](https://github.com/lopes/dotfiles) so the next steps would be easier to implement.  Note that I use Zsh as the default shell and Arch comes with Bash by default, so I had to change the default shell, logoff, and login so the dotfiles would be applied.

```sh
chsh -s /bin/zsh
```

The hardening steps are described in more depth [in this post](https://lopes.id/hardening-arch-linux/), but I made some changes.  Note that after the hardening, the root user was disabled, so I reinstalled my dotfiles for the new user's profile.  Proceeding with the changes, disabled the `remote-fs.target` service because this machine would not need to use this kind of file system.  Next, I created the `~/.config/zsh/.zlogoff` file to clear the history of commands after logging out.  This can be done by inserting the following command line into that file.

```sh
:>$HISTFILE
```

## Automatic Updates
I wanted to grant that the system would be frequently updated, so I created a service only for that purpose and made it be executed at boot time.  It consists of a configuration file that is invoked by systemd, so create the proper file into systemd's path (`sudo vim /etc/systemd/system/updater.service`) and insert the following content into the file:

```
[Unit]
Description=System Updater
[Service]
ExecStart=/usr/bin/pacman --noconfirm -Syu
[Install]
WantedBy=multi-user.target 
```

Now give execution permissions to the file and enable the new service using the `systemctl` utility:

```sh
sudo chmod 755 /etc/systemd/system/updater.service
sudo systemctl enable updater.service
```

This is a nice method to update the OS because despite forcing updates at every boot, the update itself occurs in the background which does not degrade the system's performance (considering a reasonable hardware configuration).  To troubleshoot this service (or any other), use `journalctl` with the `-u` flag.

```sh
journalctl -u updater.service
```

After doing all of this stuff, I performed a vulnerability check against this machine to grant that no obvious mistakes were made and finished the job.  It is important to notice that this machine remains off until it is needed to perform such tests, so the risk is highly reduced.  However, it is possible to create a cron job to run the `shutdown` command now and then to make sure it will be turned off.  If someone is working, the command can be canceled with `shutdown -c`.
