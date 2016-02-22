---
layout: post
title: Migrating to Atom
description: The reason I left Vim for Atom
tags: [thoughts, atom, vim, computing]
modified: 2016-02-22
image:
  feature: thoughts.jpg
  credit: ensc
  creditlink:
---

Recently I stumbled across the editor [Atom](https://atom.io/), and it was amazing!
I am often accused of being a "command-line guy", and I disagree, I love the graphical interface, using the mouse for certain tasks, and yet enjoy using [vim](http://www.vim.org/) and the command line interface all the same. So here here is the reasons why I'm now deciding to leave the command line editor vim in favour of a graphical editor.

## The Ideal Black Box Model
I have slowly over the years formed a Ideology of what the a tool or a set of tools should be like. Though neraly everyone in the modern era has embraced the idea of the "abstaction as a black box", but I think there should be more to the black box model than simply hiding stuff from view. There should be some thing that people should go the extra mile of designing to try and get most out of the box they spent so much time on, to be able to make the tools we have evolve as we find or require more stuff.

### Designing for the user
The interface should be clear, intuitive and and well documented, so it should have the properties that:

   1. **Definite behaviour**: For some a difinitive input, there should be a definitive and fixed output
   2. **Ease of access**: Anyone should to be able to get their hands on it without direct aid from the developers, or opening the black box.
   3. **Black boxes don't cast shadows**: All flow in and out of the box should be transparent.

For software design, the first point this is typically achieved in all every well-known software (as to the so-called "interface" design in physics analysis, that a completely different rant all together...). But there should be more to the second point then some people think: to "anyone" means that the tool should be adaptable to suit the perculiarities of the audience using your tool, both for habits of the user in question, and for the various task they wish to perform.

Of course, you don't have to design for anyone, and your program will forever have such a limit user base. Then, there is the "apple-style" approach, work very hard to find a good pre-set that works well for a lot of people. But then as your audience grows, and develops new habits, and demand more out of their tool, it is completely up to the deveoplers to keep one step ahead of their users, and this approach is requires the foresight that not development team could have (even giants in this philosophy like Apple flops very badly with some of their products).

I'm more of a fan of the appraoch that the developers grant the freedom to the user to modify the the tool to some extent. Since the tool's design should be for the user, it makes sense that we should allow the tool to be tune for their needs, and to learn from what they tune to further imporve the experience of users. [Firefox](https://www.mozilla.org/en-US/firefox/new/)'s [smooth scroll](http://www.pcworld.com/article/221150/Firefox.html), [Atom](https://atom.io/)'s [snippet package](https://atom.io/packages/snippets), [Kerbal space program](https://kerbalspaceprogram.com/en/)'s improved aerodynamics system are all great examples of this. Interaction between the developers and the user is what pushes the tool to evolve in the direction that is benefical as a whole, removing the blind spots that the devs might have when fixated on performance and raw numbers alone.

### Opening the black box
If we accept this model for making the program, the why don't we go the extra step? Instead of just letting the user added stuff via a predefined interface, we should allow the user to enter the box itself and allow the to modify the black box itself, to further blur the boundary between the developers and users. This is the final point that I found so appealing about the open-source movements, that tools are truely being design for the user, that the user are empowered by the tools they want for themselves.

Of course to allow this to work, even more documentation and maintenance is required, to keep everything clean and clean for people that are new to you project to understand (as for physics analysis... that would be yet another longer rant...). But all of what I said is not limited to command line tools. But is might be still valid that I am commonly mistaken for a command line guy: tools that fit the criteria above are so much more common in command line interfaces. Whether this is due to the longer history or the hassel of having to design for the graphical interfaces, I don't know, but the tools in command line tools are seems much more complete and mature and powerful and general. But then, alas, tools will always have their limitations. And I think I have found the missing elements I have crave for so long in vim to appear in the editor atom.

## The appeal of Atom
Let's start with the annoying little annoying things about using vim.
The first is that vim's display is all character based (this is true even for gvim). So try as I might, there are just some things that don't look as nice in vim.

<figure>
   <img src="http://gdriv.es/ensc_github/vim_layout.png"/>
   <figcaption>My vim layout at the time of writing</figcaption>
</figure>

Little things, like icons for file type indicators, minimap of the file you are editing, latex formula previews, markdown document  previews just isn't impossible in vim. Does this make vim any less of an text editor? Not really, but it's just things that I wish where possible to make my workflow easier.

Then there is the [Damn You Muscle Memory](http://tvtropes.org/pmwiki/pmwiki.php/Main/DamnYouMuscleMemory) moments when moving from application to application. Vim's keystrokes for executing commands is... unique. Not doubt it is efficient for those very familiar with it, but when your work requires you to move from application to application, it can get very infuriating getting your fingers to perform different motions in diffent application. I'm not that a big fan of the the hyper efficient keystrokes, but still wish to retain that precise motions you can perfom in vim by exiting the edit mode and using commands/hotkeys.

Is vim a bad editor? No! It's [awesome](http://vimawesome.com/)! There are just some aspects of it that doesn't sit right with the workflow I wish I could have.

Then I was introduced to atom:

>   Atom is a text editor that's modern, approachable, yet hackable to the core — a tool you can customize to do anything but also use productively without ever touching a config file.

It was like the editor that works so well with what I want to do! Simple typing for simple, text based task, yet having the precision of a command line editor when I need such functionalities. All open-source and really hackable to the core, with tonnes of stuff that I could add to truly make the experience my own!

## Difficulties and closing words
Not all is fine about atom though. I still miss it's direct and straight forwards interface with the file system a lot better. I never quite got why someone would want to open files based on anything other than a working directory. I'm still in the [damn you muscle memory](http://tvtropes.org/pmwiki/pmwiki.php/Main/DamnYouMuscleMemory) phase when editing text. Some plugins are nowhere near as powerful and mature as the plugins found at vim awesome (main complaint I have is the non-programable snippet system). The majority of people migrating to atom are web developers, meaning that a lot of C/C++ related plugins are rather slowing in development. But for all of that, I think it is time to move to this new environment!

> Would vim be gone?

On my system, I will still be using it to system related stuff since it works so much better when moving around the file systems, but now atom would be my primary editor to the larger projects I work on. As my brother said:

> Vim is no longer just a program, it is a standard people set when comparing what a text editor should be able to do.

Like what unix did to operating systems, TeX for typesetting systems, vim(and emacs) is doing the same for text editing.
