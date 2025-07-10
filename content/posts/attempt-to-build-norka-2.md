---
title: "Attemp to build Norka 2"
date: 2025-07-10T17:55:38+03:00
draft: false
tags: norka, markdown, what's new
description: "I've been improving my note-taking app Norka for over a year, transitioning from Rust and C# to Python for its speed and robust GTK ecosystem, and now focus on Norka 2 with features like workspaces, nested pages, rich formatting, sharing, cloud sync, encryption, LLM integration, tasks, and advanced search, aiming for ongoing development and early access."

---

## Introduction

For more than a year now, I have been periodically returning to trying to rethink my note-taking application, [Norka](https://flathub.org/apps/com.github.tenderowl.norka), to update the interface, add modern features, and finally add synchronization with the cloud.

I've had several attempts to use Rust to add speed and reliability to the application. I use C# ([Gir.Core](https://gircore.github.io/)) for the same reasons and because I love this language. But unfortunately, in the end I returned to Python, because it and [PyGObject](https://pygobject.gnome.org/) provide the necessary development speed and support a lot of libraries that I need for my work. The Python development ecosystem for GTK looks the most mature and complete.

So, after a year of experimentation and prototyping, studying analogs and my application requirements, I started another attempt to implement Norka 2.

## Inspiration

I have been working with macOS for more than 10 years and I believe that it is for this OS that the most beautiful and user-friendly applications have been created from the point of view of UX. Let's leave beauty out of the box, as it's a matter of taste, and turn to user experience.

I have tried working with many applications, especially considering those that have received awards for their design - Craft, Ulysses, Bear, Evernote, IA Writer, etc. I also looked at various online services such as Microsoft Loop, Confluence (and why not?) and others . At some point in time, I caught myself thinking that 2 services had become the most convenient for me: craft.do as a desktop application and web service, and Microsoft Loop as a web service. It was decided to assemble the functionality of the new version of Norka from them, namely:

* Workspaces
* A tree structure where each page can have nested pages.
* Customization of the design of pages and spaces: icons and covers help with work and just make work more fun.
* Instead of Markdown, use rich formatting in the document itself, like office software.
* The ability to publish pages, share them with other users, or make them publicly available to everyone.
* Sync with the cloud.
* Encryption of spaces and pages with password access for published pages.
* Integration with LLM both locally and externally (whether [Ollama](https://ollama.com/) or OpenAI)
* The ability to create tasks, as implemented in Craft.
* Advanced search capabilities, calendar linking, etc.

Of course, this is not the final list, and it will be updated. However, it's a great set to start with :)

By the way, you can follow the updates on my [Twitter](https://x.com/meamka) or on the project's [Github](https://github.com/TenderOwl/NorkaXT).

## Workspaces

The first thing to do was to sort out the workspaces. The task they are designed to solve is to group pages by topic, project, or otherwise. The user can decide for himself, the main thing is to give him the opportunity. And at the same time, make it possible to add an icon, cover, and control functionality. So we get something like this:

![Workspace creation dialog](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/w67etd46k6xuvkohbebf.png)

The user can select an emoji icon and a cover from the preset images. However, in the future I plan to add integration with online services such as [Pexels](https://www.pexels.com/) or [Unsplash](https://unsplash.com/).

Workspaces contain pages that are deleted when the space is deleted. So be careful 🙂

## Pages

If workspaces are complementary functionality, then creating and editing text is the main one. Therefore, it will require the most work on itself, but let's see what has already been implemented.

### Editing

Surprisingly, the fact is that pages can already be created and their contents edited. At the same time, syntax highlighting is now being used, which migrated from the first version of Norka - Markdown markup. In addition to this, I started implementing work with formatting tags, without having to use a markup language. My experience suggests that this is a more convenient option, especially if you combine it with Markdown - use markup during typing as shortcuts for tags. For example, if a user types
```
# The Awesome Title
```

, then this text will be automatically converted to a title, and the # sign at the beginning of the line will be removed like this:
```
The Awesome Title
```

This should work for titles, links, images, and other markup elements.

And if the ability to insert tags was not particularly difficult, then the issue of saving and loading required analysis and study, since the standard functionality of Gtk libraries allows you to save text, but there is no formatting in it. After trying out various data serialization options, I came to the decision not to mix content and formatting, and these two parts of the same whole should be stored separately from each other. This is about how documents are stored `.docx` and `.odt`.

### Nesting

You can also nest pages inside each other and move them between workspaces. This can be done either by simply dragging the mouse or using the context menu.

The nesting is not limited, but in the future this parameter may be revised after analyzing the work on large volumes and deep nesting of pages.

## To be continued

A start has been made, but there is still a lot of work ahead, both in adding new functionality and debugging interfaces and performance. It's too early to talk about release plans, those who suffer can download the source codes and build the application themselves, everything is available on GitHub.

**See you!**
