---
layout: post
title: "Getting Started with Jeykll"
description: "I'm starting a website using Jekyll! But Why?"
tags: [computing, website, video]
modified: 2015-09-07 
image:
  feature: abstract-7.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

I have finally began a website to store by personal thoughts! 
But out of all the platforms available out there, why [Jekyll](http://jekyllrb.com/)?
What is it that this platform is chosen among the others.

# What I wanted
With the abundance of social media, there seems to be no end to place to post personal thoughts and whatnot on the internet. 
I all ready have [facebook](https://www.facebook.com), [google+](https://www.google.com/) and [plurk](https://www.plurk.com/), 
so why go out to seek a new platform? 

What social media fails to provide for me is the ability to organize and navigate the post efficiently,
to be able to find previous post on a certain topic easily, since I would also want to post some more aesthetically appealing work logs 
that I would want to go back to in the future. Also the flexibility to stylize my website would also a bit plus.

But wouldn't blogging platforms such as wordpress, blogger and many others blogging platforms be sufficient? 
Here comes the more interesting part in my search for the platform of choice.
Since I'm a bit of a nerd...:

### On the posts:

* Generate latex style equation:

$$
\mathcal{L}_{QED} = \bar{\psi} \left( i \not D + m  \right) \psi - \frac{1}{4} F_{\mu\nu} F^{\mu\nu}
$$

* Syntax aware code snippets
{% highlight c++ %}
#include <stdio.h>
int main( int argc, char argv[][]){
   printf("Hello World\n");
   return 0;
}
{% endhighlight %}

These are two important yet uncommon functionalities for me.
While it is theoretically possible add these on to most blogging platforms such as google blogger,
the solutions are normally a bit too hacky and difficult to maintain.

### For the main framework:

* Direct access to any appearance changes to the website. Preferably through text interface and not GUI. 
* Could be hosted offline for personal usage if desired.

The first is available on nearly all blogging platforms in principle, 
but there is a wide spectrum as to how easy it is to access and maintain custom HTML/CSS code.
There are also many blogging platforms that allows you to pack-up your website, for download, but 
not fewer platforms allow you to upload what ever code you want.

And finally, as an added option:

* Command lined based editing and deploying

Of course, all of this could be achieve by writing a fully-fledged dynamic website, 
but I'm not wanting to go though this sort of effort just to blog.
Also I lack the resources to host a website publicly on my personal machines, so I began browsing around to find what will fit my need.


# What I came across:
The posting requirements are met by the familiar [git-flavoured markdown](https://help.github.com/articles/github-flavored-markdown/)
that is used to write README files on GitHub, also I have a rather pleasant experience of using markdown
and think it is much simpler to use that raw HTML or latex for casual writing. 
To began browsing for blogging solutions with markdown:


### [Logdown](http://logdown.com/)

Claims to be *the* bloging platform for hackers, and for good reason:

* One of the best WYSIWYG editor with markdown support.
* Git-flavoured markdown, latex support.

These are very attractive features, but:

* Browser interface file management is not very simple for handling large amounts of files
* Free account has very limited space to work with.

### [Ghost](https://ghost.org/)
Markdown support seems to be its key feature, but [pricing](https://ghost.org/pricing/) isn't ideal for a student. So I didn't even get to try it out. 

### [Dropplets](http://dropplets.com/)
A simple open-source program to deploy on your web servers. Again, not being a platform is a problem for me, 
but the concept of this program got me thinking: essentially what I need to to transform my markdown files into a website right? 
There should be other solution that I could find online?

### [Gitit](http://gitit.net/)
A git based, lightweight version of [MediaWiki](https://www.mediawiki.org/wiki/MediaWiki)(the backend of Wikipedia).
This searches for files in a git repository and generates a site based on the informating written in the file. 
It is far more lightweight than MediaWiki, which requires a beefy data-base backend, 
but Gitit itself is still very beefy, and isn't maintained very actively, making a good starting theme dificult to find.

By finding the last two, I was beginning to see that term for the solution that I wanted: Static Site Generators.
Generators that parses text-files into HTML file to be presented on the internet. After looking into static site generators,
I found that the solution has been staring in my fact everytime I go to GitHub....


## The Choice: GitHub Pages  + Jekyll
[Jekyll](http://jekyllrb.com/) is used by github to change the source code files in a repository in to a clean, browser friendly interface. 
[GitHub pages](https://help.github.com/articles/using-jekyll-with-pages/) is essentially a special repository that allows the user to 
directly interact with the Jekyll engine to produce their own websites! 
While github pages could be deployed per repository, you could also dedicate a repository entirely for a page!

There are some loose ends to tie up:
  
   * *Could I locally deploy the website for testing?* 
   Yes, Jekyll could be installed locally, and eat very little amount of resources when the generator is not running.
   For reference see [here](https://help.github.com/articles/using-jekyll-with-pages/).
   * *Could commenting be added?* 
   Yes, using JavaScript to communicate with Google API, with the pages unique url as it's ID, it is possible for commenting per post.
   For how this works read [here](http://harleykwyn.com/google-comments-in-jekyll/)
   * *How large could my website expand?* 
   Unlimited. GitHub has no hard-cap on [repository size](https://help.github.com/articles/what-is-my-disk-quota/), and 
   since website are text based, it would be hard to reach the recommended limit of 1GB.
   * *Command Line interface?* 
   It's GitHub! What do you expect? 

Completely setting up the website wasn't as easy as I had thought, 
Markup language with code isn't easy to read at the best of times, and I have forgotten quite a lot about HTML and CSS. 
A lot of trial and error and pointless commiting just to see what works was required, But I got it up in the end! 
Hopefully this is a framework that I could continuously build on, and if I have the chance, maybe get to design more part of this in the future
