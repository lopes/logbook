+++
title = "My Minimalistic Approach to Mac System Setup"
date  = 2022-09-30
description = "Learn how to setup your Mac system with fewer apps and keep it simple."

[taxonomies]
tags = ["unix", "apple"]
+++

As a long-term user of Macs (since 2008) my core system changed over the years, but the target remains the same to me: Keep it simple.  I'm not a fan of having many apps, some of them that I barely use, for security reasons.  Since I want to keep my system always up-to-date, the more apps I have, the longer and more bandwidth it will take to keep them updated.

I dropped [office suites](https://en.wikipedia.org/wiki/Productivity_software#Office_suite) more than a decade ago because [Google Workspace](https://workspace.google.com/) gives me everything I need in terms of document processing ([Overleaf](https://www.overleaf.com/) for more elaborated docs --shout out to [LaTeX](https://www.latex-project.org/!)), presentations, and spreadsheets ([Google Sheets](https://en.wikipedia.org/wiki/Google_Sheets) is far better than [MS Excel](https://en.wikipedia.org/wiki/Microsoft_Excel), but most people are not prepared to read this).  This movement saves a lot of space on my computer, by the way.

The breaking news is that I dropped [Google Chrome](https://en.wikipedia.org/wiki/Google_Chrome) in favor of [Brave Browser](https://brave.com/).  I'm not sure if I will be able to get rid of Chrome forever, but it seems a good try on my part.  So, having a fresh install of [macOS](https://en.wikipedia.org/wiki/MacOS), here's what I do.


## Homebrew
[Homebrew](https://brew.sh/) is a **must** for Mac users.  It's not only a Swiss Army knife for IT professionals, but it's the best software management tool for Mac, IMHO --even better than AppStore itself.  Homebrew has lots of software in its list, including some of the most popular, like browsers and text editors.

Installing homebrew is not an easy task for ordinary users, though.  Since it requires using the terminal, many people could get confused, but it is as easy as running this command: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`.

Homebrew was inspired by package managers in Linux, like [APT](https://wiki.debian.org/Apt) and [RPM](https://rpm.org/), but in my opinion, it surpassed them.  The only issue for me is that it is written in Ruby, so it's quite slow sometimes.  I imagine if it was re-written in Rust and the recipes used JSON, how good it would be.


## The Rest
Having Homebrew eases the rest of installation tasks because every software I use is covered by Homebrew.  Here's the list:

- [Brave Browser](https://brave.com/): Good appeal and reviews.
- [The Unarchiver](https://theunarchiver.com/): Uncompress anything.
- [iTerm2](https://iterm2.com/): Terminal is fair, but this terminal emulator is better and lightweight.
- [AppCleaner](https://freemacsoft.net/appcleaner/): It removes some junk files installed by softwares outside Homebrew and some software extensions either.
- [Transmission](https://transmissionbt.com/): Simple and effective.
- [Visual Studio Code](https://code.visualstudio.com/): A bit heavy, but the best text/code editor.
- [VLC](https://www.videolan.org/): Plays everything.
- [Spotify](https://open.spotify.com/): Because music.
- [Notion](https://www.notion.so/): Excellent tool for notes and productivity, getting things done.

```
brew install \
    brave-browser the-unarchiver iterm2 \
    appcleaner transmission visual-studio-code \
    vlc spotify notion
```


## CLI
I don't spend much time setting my GUI up, only a few things, like dark mode system-wide, auto-hiding task and menu bars, and removing any icons from my desktop.  I prefer to use my apps maximized on virtual desktops and switch between them using the three-finger swipe.

Regarding CLI, my approach is different, because I like to customize my environment, to make things easier for me in the middle and long term.  That's why I keep my [dotfiles](https://github.com/lopes/dotfiles) in a GitHub repository: I can easily set my system across multiple machines.  The README has all the instructions to apply the dotfiles, but it's composed of only 4 commands.  The highlights for me are:

- Folder distribution is organized and compliant with some best practices.
- Multiplatform environment: Works in Linux and BSD (macOS).
- Easy place (folder) to put binaries for the user.
- zsh autocompleting in case insensitive mode is gorgeous.
- Useful aliases.
- Does not require further software.
- Simple and clean, good for high performance.

For CLI, I also use to install [micro](https://github.com/zyedidia/micro/releases) and [Zola](https://github.com/getzola/zola/releases), and I do that by simply downloading the executables and putting them under `~/.local/bin` because my dotfiles setup makes it work.


That's it!  Core system alive and kicking for the next jobs!  Avante!
