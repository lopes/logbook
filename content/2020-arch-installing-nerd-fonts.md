+++
title = "Installing Nerd Fonts on Arch Linux"
date = 2020-07-27
description = "Learn how to install and use Nerd Fonts, a curated collection of fancy font families, focused on developers, on your Arch Linux system."

[taxonomies]
tags = ["unix", "arch"]

[extra]
image = "images/logos/archlinux.png"
+++

I am setting up my graphical environment in Arch Linux, but since the installation is minimal, I noticed I needed to install TTF/OTF fonts.  The first font I installed was DejaVu, which is pretty good, but I wanted something more modern, but with good support from different characters (Latin) and glyphs.

While searching the internet, I stumbled upon [Nerd Fonts](https://www.nerdfonts.com/), a curated collection of many fancy font families, focused on developers.  All fonts are patched to provide support to popular glyphs, which makes them perfect for use throughout the system, mainly in terminals.  That was all I needed.


## Installation
The Nerd Fonts repository is **huge** and even the author does not recommend cloning it.  That said, the best way is to select the ones that you want and install them manually.  Since I am currently testing my Arch installation in VirtualBox, I decided to document this process so I can repeat it in the near future.

The first thing here is to create a directory to house your downloaded fonts and either install all of them or select the ones you want and install them.  The next listing shows how to do this, but you must know if you want to install the whole thing or just a part of it.

```sh
mkdir fonts; cd fonts

## to install all fonts
curl -sL https://github.com/ryanoasis/nerd-fonts/releases/latest | egrep -o "/ryanoasis/nerd-fonts/releases/download/.+\.zip" | sed 's/^/https:\/\/github.com/' | wget -i/dev/fd/0

## to download a list and pick only the ones you like
curl -sL https://github.com/ryanoasis/nerd-fonts/releases/latest | egrep -o "/ryanoasis/nerd-fonts/releases/download/.+\.zip" | sed 's/^/https:\/\/github.com/' > list.txt
vim list.txt       # edit the list to remove undesired fonts
wget -i list.txt
```

Having all the fonts locally, unzip them into the local fonts directory, update the cache files for allow applications to use the new fonts, and clean everything --if you want to make a system-wide installation unzip in `/usr/local/share/fonts`.

```sh
unzip "*.zip" -d ~/.local/share/fonts
fc-cache -fv
cd ..; rm -rf fonts
```


## Testing
All fonts are currently installed on the system and ready for use.  Now you can check their names using the next command --it will mix system-wide with local user fonts.

```sh
fc-list | cut -d: -f2 | sort | uniq
```

Those names are the ones you will use to configure your system.  Now you can easily test them using the next command --change the font name appropriately.

```sh
echo "The quick brown fox jumps over the lazy dog" | pango-view --font "Cascadia Code" /dev/stdin
```

At this point, you should have all fonts ready to use.  An improvement could be setting up the `fontconfig` file and you can use [mine](https://github.com/lopes/dotfiles/blob/master/.config/fontconfig/fonts.conf) as an example.

See ya!
