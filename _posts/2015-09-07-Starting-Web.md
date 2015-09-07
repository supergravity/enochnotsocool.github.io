---
layout: post
title: "Getting Started with Jeykll"
description: "I'm starting a website using Jekyll! But Why?"
tags: [Computing, Websites]
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
Since I'm a bit of a nerd, I would like the platforms to provide: 

* Post latex style equation, like:

$$
E^2 = p^2c^2 + m^2 c^4 
$$

* Syntax aware code snippets
{% highlight c++ %}
#include <stdio.h>
int main( int argc, char argv[][]){
   printf("Hello World\n");
   return 0;
}
{% endhighlight %}

on posts easily and efficiently. Also for the framework of the website:

* Direct access to any appearance changes to the website. Preferably through text interface and not GUI. 
* Could be hosted offline for personal usage if desired.

And finally, as an added option:

* Command lined based editing and deploying

Of course, all of this could be achieve by writing a fully-fledged dynamic website, 
but I'm not wanting to go though this sort of effort just to blog.
Also I lack the resources to host a website publicly, so I began browsing around to find what will fit my need.


## What I came across:
The first two are satisfiable with git-flavoured markdown that is used to write readme files on [GitHub](https://github.com), 
also I have a rather pleasant experience of using markdown being much simple to use that raw HTML or latex for fast text editing. 
To began browsing for blogging solutions with markdown. A pondered a bit at the solutions such as Ghost, Dropplet and Logdown, 
but the free accounts for Ghost and Logdown couldn't provide the flexibility and expansiveness that I would like and Dropplet
requires me to host my own server.

In due time I came across MediaWiki (the backend of wikipedia) 
and Gitit( a wiki generator using Git information). But what I wanted wasn't exactly a wiki. 
Also, the idea that I was running a database software just to blog wasn't very appealing to me.

Finally, I came across the service that I have been using for quite some time: Github. 
And rather than the ''blogging with markdown'' solution that I had been looking for, I realized that what I wanted is exactly what GitHub pages 
provided: Static website generators.


## The solution?
Information on a post/page would be stored in plain text format on the top of the text file, 
and the generator uses this information to generate HTML files that would be passed to the browser. 
No heavy duty databased software would be required during the website browsing, only (possibly) during the generation process.
The only user interaction that I require during browsing time is with the commenting section, 
and that could be hosted by other services (facebook, google, disqus) via javascripts.

I took a look on how to interact with the static site generate that GitHub uses. 
Jekyll seems straight forwards enough to deploy right now with my limited knowledge of HTML, CSS and javascript, 
and expansive enough to do more fancy stuff if I feel the need to in the future.
Being hosted on GitHub also means I get no hard-cap on the size limit, and since the website would be text orientated, 
it would take some time to reach the supposed 1GB recommended size limit.
What's more, being linked to GitHub, means that all the editing work could be done within the command line interface! 

So I started writing this website in the Jekyll framework.
