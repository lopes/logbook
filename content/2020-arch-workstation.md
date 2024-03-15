+++
title = "Arch Linux Workstation Setup"
date  = 2020-07-15
description = "Install graphical environment and apps, setup configs in your workstation."

[taxonomies]
tags = ["unix", "arch"]
+++

Now that the Arch Linux is [installed](https://lopes.id/installing-arch-linux/) and [hardened](https://lopes.id/hardening-arch-linux/), it is time to install a graphical environment to enable this system to be used as a workstation.  This text will show how to setup the system, install the basic packages, and apply my personal configurations to build a system very close to mine.

Before starting, make sure the system is up-to-date with the next command.

```sh
sudo pacman -Syu
```


## Drivers
The very first thing to do is to guarantee that all hardware is properly recognized and maybe it will require the installation of drivers.  This text will not cover driver installation, since it varies from machine to machine.

Video card, sound card, keyboard, mouse, peripherals... all of them should be managed by the system, but fortunatelly Arch's documentation covers a lot of systems, and after that you can look in Arch's forums, Reddit, or even Stack Exchange.

### VirtualBox
This text will only cover the scenario proposed on the first text, a VirtualBox VM, so it is unnecessary on bare metal installations.

Start by installing VirtualBox's basic packages and enabling its service in Arch.

```sh
sudo pacman -S virtualbox-guest-utils xf86-video-vmware
sudo systemctl enable vboxservice.service
```

In my tests the VirtualBox's video card was not able to dynamically set the video resolution, so I had to discover the one used in my system (1440x900), match it to the options provided by GRUB, and apply them in GRUB.

{% admonition(type="note", title="Note") %}
To see a list of GRUB's available resolution options, type `control-c` while in GRUB screen and use the command `videoinfo` for GRUB EFI or `vbeinfo` for GRUB BIOS.
{% end %}

To apply the desired resolution, open the file `/etc/defaut/grub`, locate the option `GRUB_GFXMODE` and insert the resolution there.  In my case the result was: `GRUB_GFXMODE=1440x900x32`.

Now, regenerate the GRUB configuration file and remember to edit it to add the `--unrestricted` option so [GRUB will not ask for password](https://lopes.id/hardening-arch-linux/) for default boot.  Next listing shows the commands, including the necessary reboot.

```sh
grub-mkconfig -o /boot/grub/grub.cfg

vim /boot/grub/grub.cfg
...-menuentry "Arch Linux"... --unrestricted...

reboot
```


## Graphical Environment and Applications
Install the base graphical environment and applications with the next command, but be sure to adapt it to your personal usage.

```sh
sudo pacman -S \
    xorg-server xorg-xinit ttf-dejavu xorg-xbacklight \
    picom xautolock i3-gaps i3lock i3status rofi \
    dunst libnotify \
    pulseaudio mpd mpc pulsemixer pavucontrol \
    arc-gtk-theme papirus-icon-theme \
    xdg-user-dirs \
    termite code ranger pcmanfm \
    firefox transmission-gtk \
    gnome-calculator calibre evince \
    nitrogen maim xclip xdotool \
    imagemagick gimp inkscape feh vlc \
    nmap wireshark-cli tcpdump httping
```

Having all applications, prepare the system to receive the configuration files.  Here I will show how to install my own dotfiles, but you can change it according to your preference.

```sh
cd    # go $HOME now
mkdir ~/{Desktop,Documents,Downloads,Laboratory,Music,Pictures,Shares,Videos}
mkdir ~/Pictures/{wallpapers,screenshots}
mkdir -p ~/.cache/zsh

git clone https://github.com/lopes/dotfiles

cp -r dotfiles/.{bashrc,config,inputrc,local,screenrc,vimrc,xinitrc,zshrc} .
```

Now install addional local software, like micro and Zola.  The `~/.local/bin` directory is set in my dotfiles to store this kind of software and is included in the user's path.

```sh
mkdir .local/bin
cd .local/bin
curl https://getmic.ro | bash
```

### Fonts
You will need to install additional fonts to use my dotfiles and I recommend installing the Nerd Fonts collection.  Follow [this link to read a step-by-step guide](https://lopes.id/intalling-nerd-fonts) on how to install Nerd Fonts on Arch Linux.

```sh
reboot
startx
```

{% admonition(type="note", title="Note") %}
To troubleshoot Picom, run the same command in i3 configuration file and pay attention to the error message.  Maybe you will have to disable some feature for instance `vsync`.
{% end %}

### Keyboard

```sh
setxkbmap -layout us -model abnt -variant intl
localectl --no-convert set-x11-keymap us abnt intl
localectl status
```

### Audio
To troubleshoot some audio problem, you can try running `pulseaudio --check` and `pulseaudio -D`.

### Wallpaper and Notifications
Download wallpapers and put in `~/Pictures/wallpapers`.  We're gonna use [Nitrogen](https://wiki.archlinux.org/title/nitrogen) as a tool to set up the wallpapers: `nitrogen ~/Pictures/wallpapers`

Dunst is not delivered with an elegant way to reload the configuration file, so the best way to do it is killing Dunst's process with `killall` command.


## Theming
Firefox theme is set with GTK, but there is an Arc-Dark theme for it [here](https://addons.mozilla.org/en-US/firefox/addon/arc-dark-theme-we/).  Download Arc Dark theme [here](https://addons.videolan.org/p/1167642/) and install like this: `vlc -I skins2 --skins2-last ~/.local/share/vlc/skins2/Arc-Dark.vlt`.


## System Performance
Until now we installed the system using secure features and hardened the whole thing.  Now we implemented a lot of graphical software and it worths measuring how the boot process was affected.  The commands below show that and can be used to troubleshoot whether there is some problem in the boot process.

```sh
systemd-analyze
systemd-analyze blame
systemd-analyze critical-chain
systemd-analyze plot > /tmp/boot.png
```

{% admonition(type="note", title="Note") %}
To monitor processes consuming power, a.k.a., draining the battery, you can use [powertop](https://wiki.archlinux.org/index.php/Powertop).
{% end %}


## To be Continued...
Setting up a beautiful and usable desktop environment in Arch with i3 is far from an easy task.  At this point, I got an usable and somewhat concise environment, but there are some bugs to be fixed and some configurations to be done.

### Todo-list
- Configure tmux
- Create .local/share/application .desktop files
- implement sound tools and i3 controls
- multimedia keys with xmodmap

### Bugs
- Bug: Nerd Font glyphs are too small
- Bug: Picom blurring not working
- Bug: Make Qt use GTK theme


## References
- [Arch Linux's VirtualBox](https://wiki.archlinux.org/index.php/VirtualBox/Install_Arch_Linux_as_a_guest)
- [Arch Linux's Kernel Parameters](https://wiki.archlinux.org/index.php/Kernel_parameters)
- [Arch Linux's Xorg](https://wiki.archlinux.org/index.php/Xorg)
- [Arch Linux's Picom](https://wiki.archlinux.org/index.php/Picom)
- [Arch Linux's i3](https://wiki.archlinux.org/index.php/i3)
- [Arch Linux's Fonts](https://wiki.archlinux.org/index.php/fonts)
- [Arch Linux's Keyboard Configuration](https://wiki.archlinux.org/index.php/Xorg/Keyboard_configuration)
- [Configuring and Understanding Zsh](https://thevaluable.dev/zsh-install-configure/)
- [A Detailed tmux Walkthrough to Boost Your Productivity](https://thevaluable.dev/tmux-boost-productivity-terminal/)
