---
title: "Elementary Os Contractor in Action"  
date: 2021-08-28T17:42:31+03:00  
draft: true  
description: "Tutorial shares the experience on how to use  WebSockets with Python and GTK"  
---


# Intro

Almost two years ago I lost my iMac and it brought me an idea to see what's changed in the Linux world for 10 years. Starting from Ubuntu and Fedora I finally came to the elementary OS project's website. And it was quite astonishing. In comparison to the other distributions elementary had its view on how the operating system should view, how to make apps, its own design language and [developers documentation](https://docs.elementary.io/develop/). Thereby the decision to move to this OS was easy.

For now, it is almost two years since I moved from macOS to elementary OS as my daily driver. And, as I said earlier, I'm a developer. After looking for a native text editor I made myself think to make [Norka](https://tenderowl.com/work/norka/). But, this article is about the distinct application. Actually, I'm not the author of it, the app is already a part of elementary itself - [Contractor](https://github.com/elementary/contractor).

# Contractor

The idea is very simple: users can write a contract - a file which instructs the Contractor how to call the command - and it comes available in any supported apps: Files, Photos, etc. 

# Action

I suppose you're already got the idea of how the Contractor can help you in your daily work, but let's make a contract that... well.. help you share any file via Telegram. What do we need for it?

- install Telegram
- get the command
- create a contract
- test it!

Because it differences in one case of installation on another I will write for the [Flathub version](https://flathub.org/apps/details/org.telegram.desktop). 

Let's dive in!

## Install Telegram

As of [elementary OS](https://elementary.io) user, I can simply go to flathub.org and press the "Install" button. Sideload app do the job. But what about other distros? Well, the right way is completely covered on the Flathub site so we don't wanna go through all steps :)

## Get the right command

To write a good contract we need to find out what command does the job we need. In some cases, it could be easily extracted from `--help` info or `man` pages. Luckily Telegram command-line switches are fully described in the GitHub Wiki: https://github.com/telegramdesktop/tdesktop/wiki/Command-Line-Switches. `-sendpath <file>` - the desired switch.

One part is done, let's figure out the second one. To run the application with a command-line switch installed via Flatpak we have to call `flatpak run` command. Thus our command will look like:

```bash
flatpak run org.telegram.desktop -sendpath %f`
```

This is already enough to make a contract!

## Contract



## Bonus

Sometimes you wanna indicate to yourself when the contract will be completed. To make it happen it's not enough to call just one command, you need to send a notification somehow. Again, elementary already has an application for it named `notify-send`, and all we need is to combine all the commands into one with help of `sh`. As you may see in the Tips section it is easy enough:

```ini
Exec=sh -c "flatpak run org.telegram.desktop -sendpath %f && notify-send 'File sent' '%f successfully sent via Telegram'"
```

Final version:

<script src="https://gist.github.com/amka/8bf9b556ce4e0f0a04013b8b6718560f.js"></script>

# Tips:

- Use pkexec to ask for root permissions. Example: `Exec=pkexec chmod +x %U`
- To call multiple commands at once try to use sh: `Exec=sh -c "echo 'step 1' && notify-send 'Done' && echo 'step 2'"`
