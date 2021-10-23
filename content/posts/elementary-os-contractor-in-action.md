---
title: "Elementary Os Contractor in Action"  
date: 2021-10-24T00:04:31+03:00  
draft: false
description: "What elementary OS Contractor is and how to use it to simplify your daily routines"
---


How to use elementary OS Contractor to simplify your daily routines.

<!--more-->

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

One part is done, let's figure out the second one. To run the application with a command-line switch installed via Flatpak we have to call `flatpak run` command. Thus our command will look like this:

```bash
flatpak run org.telegram.desktop -sendpath %f
```

This is already enough to make a contract!

## Contract

Every contract should be placed inside `~/.local/share/contractor` if we talking about a specific user or inside `/usr/share/contractor` for the system-wide availability.

Simple `.contract` file example:

```ini
[Contractor Entry]
Name=Mount
MimeType=application/x-cd-image;application/x-raw-disk-image
Exec=gnome-disk-image-mounter %f
```

This example shows how to make a command that will mount disk images to the filesystem. We need something different.

Create `telegram-share.contract` file and open it in [Code](https://github.com/elementary/code) or your text editor of choice.
Change `Name` to `Share via Telegram` and `Exec` field to a command we discovered earlier. If English is not your primary language you can add localized `Name` by adding `Name[language code]` option. For example:

```ini
Name[ru]=Поделиться в Telegram
```

Last but not least we have to set what mime-types has to activate the Share command. Of course, it doesn't mean to be only disk images 
and here is the special type - `!inode` - which means any file you select no matter archive it or pdf.

Let's see what we've got!

```ini
[Contractor Entry]
Name=Share via Telegram
Name[ru]=Поделиться в Telegram
Icon=telegram
MimeType=!inode;
Exec=flatpak run org.telegram.desktop -sendpath %f
```

Now save your contract and test it by clicking on any file in Files and pressing the "Share via Telegram" option.

More examples listed in the [Contractor repository](https://github.com/elementary/contractor#examples).

## Bonus

Sometimes you wanna indicate to yourself when the contract will be completed. To make it happen it's not enough to call just one command, you need to send a notification somehow. Again, elementary already has an application for it named `notify-send`, and all we need is to combine all the commands into one with help of `sh`. As you may see in the Tips section it is easy enough:

{{<highlight ini>}}
Exec=sh -c "flatpak run org.telegram.desktop -sendpath %f && notify-send 'File sent' '%f successfully sent via Telegram'"
{{< /highlight >}}

Final version:

<!-- {{<gist amka 8bf9b556ce4e0f0a04013b8b6718560f>}} -->
```ini
[Contractor Entry]
Name=Share via Telegram
Name[ru]=Поделиться в Telegram
Icon=telegram
MimeType=!inode;
Exec=sh -c "flatpak run org.telegram.desktop -sendpath %f && notify-send 'File sent' '%f successfully sent via Telegram'"
```

# Tips:

- Use pkexec to ask for root permissions. Example: 
  ```ini
  Exec=pkexec chmod +x %U
  ```
- To call multiple commands at once try to use sh:  
  ```ini
  Exec=sh -c "echo 'step 1' && notify-send 'Done' && echo 'step 2'"
  ```
