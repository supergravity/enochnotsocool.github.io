---
layout: post
title: "Jekyll Clean up"
description: "Some code concerning cleaning up my personal site"
tags: [Jekyll, recipes ]
modified: 2016-02-03 
image:
  feature: thoughts.jpg
  credit: ensc
  creditlink: 
---

It's been a while since I have had the time to puts some content on this site, might as well begin by clearing 
up some of the stuff that where left behind from the original template and adding in some of my own style :)

## Vim colour-scheme to pygments css file
I use a modified [molokai](https://github.com/tomasr/molokai) theme for my vim editing, 
and it looks ever so nice to have this colour theme displayed for my code highlighting as well. 
Luckily someone has already given a general package for this:

https://github.com/honza/vim2pygments

### Requirements

   * [Python 3](https://www.python.org/downloads/)
   * [The hgSubversion extension](https://www.mercurial-scm.org/wiki/HgSubversion)

### Short recipe:
~~~
git clone https://github.com/honza/vim2pygments.git
cd vim2pygments
python3 vimpygments.py [your-vim-color-scheme] > temp.py
hg clone http://bitbucket.org/birkenfeld/pygments-main pygments
cd pygments
cp ../temp.py pygments/styles/
./pygmentize -S temp -f html -a .highlight > temp.css
~~~

## Generating My Theme
I when with the grey-orange-purple theme that appears in the editing view of markdown files in my vim setup.

I'm also attempting to generate my own banners, but I am still very far behind on idea's and I don't think I will 
be able to make an overall look and feel for the various categories of posts throughout this blog... 

Still, I will make what comes to mind. Hopefully they will turn out to look OK :)

Undoubtedly there are numerous bugs that I didn't manage to clear up, and there is the glaring problem with the google 
commenting system, If you notice any inconsistencies or find a fix for the google comment renderer hat could fit the 
theme more please let me know :)


## Removing old posts?
Right now, I am still not very familiar with Jekyll and the pages serve as much of a tutorial for me as they did a few months ago.
Until I get familiar enough without, I guess those pages are here to stay :/



