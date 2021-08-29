---
title: "Elementary Os Contractor in Action"  
date: 2021-08-28T17:42:31+03:00  
draft: true  
description: "Tutorial shares the experience on how to use  WebSockets with Python and GTK"  
---


# Intro

Almost two years ago I lost my iMac and it brought me an idea to see what's changed in the Linux world for 10 years. Started from Ubuntu and Fedora I finally came to the elementary OS project's website. And it was quite astonishing. In comparison to the other distributions elementary had its own view on how the operating system should view, how to make apps, its own design language and [developers documentation](https://docs.elementary.io/develop/). Thereby the decision to move to this OS was easy.

For now, it is almost two years since I moved from macOS to elementary OS as my daily driver. And, as I said earlier, I'm a developer . After looking for a native text editor I made myself think to make [Norka](https://tenderowl.com/work/norka/). But, this article is about the distinct application. Actually, I'm not the author of it, the app is already a part of elementary itself - [Contractor](https://github.com/elementary/contractor).

# Contractor

The idea is very simple: users can write a contract - a file which instructs the Contractor how to call the command - and it comes available in any supported apps: Files, Photos, etc. 

# Action

I suppose you're already got the idea of how the Contractor can help you in your daily work, but let's make a contract that... well.. help you share any file via Telegram. What do we need for it?

- install Telegram
- get the command

Because it differences in one case of installation on another I will write for the [Flathub version](https://flathub.org/apps/details/org.telegram.desktop). 

## Tips:

- Use pkexec to ask for root permissions. Example: `Exec=pkexec chmod +x %U`
