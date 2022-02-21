---
title: "What's new in Norka 1.0"
date: 2022-02-21T17:26:21+03:00
draft: false
tags: norka, markdown, what's new
description: "Norka reached version 1.0 and is now available for download. This is a brief overview of the new features and improvements."

---

# What's new in Norka 1.0

Nearly two years ago, Norka was born, a note-taking application made specifically for elementary OS and also distributed via Flathub. This version was far from version 1.0, its functionality was limited only to essential things: editing, searching, autosaving. Later, it introduced document search, export to various formats, spell checking, convenient keyboard shortcuts for editing, the ability to preview documents in HTML and statistics, etc. This was enough to start, but there was room for improvement. One possibility might be the ability to group notes in a folder and structure the user's resources in a certain way. So what's new comes in version 1.0?

## Folders

The main and most important innovation is the ability to structure documents using folders. Folders can be created by clicking on a button in the header bar or by dragging documents onto each other. In this case, you will see a window displaying a suggestion for a folder name.

Folders can be moved from one to another. The level is not limited, but there is a limit on a path length, which, by the way, are found under the title of the window in the title. Also, some characters cannot be used in the names, we plan to fix this in the next releases.

The backup function also includes the structure of the library and exports documents by putting them in the right folders. This way you will get a complete copy of the library on disk.

## New fonts and updated theme

We continue to work on using Norka not only in terms of functionality but also in terms of UI. Starting with version 1.0 Norka will use the [iA Writer Duospace](https://github.com/iaolo/iA-Fonts) font. Thank you for sharing them with the community. The color scheme of the editor has also been updated.

## Actions

In many desktop environments, it is possible to set up global hotkeys to trigger specific actions in applications. For example, take a screenshot or share a file. Now you can use the command to quickly take notes with Norka. This action can also be implemented as a command to call from hot corners in GNOME or [elementary OS](https://elementary.io/docs/learning-the-basics#multitasking).

## Specifying the language for spell checking

In one of the latest releases, we switched from GtkSpell to use GSpell, but at the same time, Norka lost the ability to specify which language to use for this check. And while this isn't a problem if you're writing in English, it creates problems for writers in other languages. This setting can now be changed in the application.

## Import and formatting fixes

As with spell checking, one might not have noticed that for non-Latin languages, hotkeys are used to format text, leading to unexpected results. For example, when inserting a title, some of the text could be erased. Now we have fixed this problem, Norka correctly inserts formatting without text loss.

We also fixed auto-completion of lists: when pressing Enter, Norka will check if an item text has been entered, and if not, then inserting the list formatting ends.

For those who use drag-n-drop to import, the problem turned into the fact that when importing several files in sequence, the documents / those that were used the first time that time were re-imported instead of the last ones. We have fixed this issue, there is no more reason to worry about erroneous imports and prevent the deletion of redundant copies.

## Conclusion

We keep improving Norka even further. We aimed to become the best writing app and note editor for Linux in the future, and possibly not only a Linux.

Thanks to everyone who uses Norka!