---
layout: post
title: "Jekyll Clean up"
description: "Some code concerning cleaning up my personal site"
tags: [Jekyll, recipes ]
modified: 2016-02-02 
image:
  feature: thoughts.jpg
  credit: ensc
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
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

## Generation of own header?
Still very far behind on idea's and I don't think I will be able to make an overall look and feel for the various categories 
of posts throughout this blog... 

Still, I will make what comes to mind. Hopefully they will turn out to look OK :)

## Removing old posts?
Right now, I am still not very familiar with Jekyll and the pages serve as much of a tutorial for me as they did a few months ago.
Until I get familiar enough without, I guess those pages are here to stay :/



