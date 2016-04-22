---
layout: post
title: Multi slides structure for reveal.js
description: My quick and dirty implementation of having multiple slides with as little overhead as possible
tags: [recipe, reaveljs, javascript]
modified: 2016-04-21
image:
  feature: code_head_1.png
  credit: ensc
---

A few weeks back, I began to take a look at [reveal.js](http://lab.hakim.se/reveal-js/) for presentation making. While the results looked fantastic, there are one key aspect that I need to mode in: The ability to keep multiple slides with the same theme with as little overhead as possible.

## What I needed and tools avaliable

I need to make slides for update reports nearly every week, so I wanted a way where **all** the styling and plugin management could be placed in a master `html` file while the contents are kept in other `.md` or `.html` files.

The ability to do so is actually already implemented in the [`markdown` plugin](https://github.com/hakimel/reveal.js/blob/master/plugin/markdown/example.html) of reveal.js, since we could change the entire contents of a slide by the changing the attributes of the `<section>` tag:

```html
<section data-markdown="example.md"
         data-separator="^\n\n\n"
         data-separator-vertical="^\n\n">
</section>
```
Here we only need to alter one line of code to change a big chunck of content in my slides!

Now that challenge was solved a bit too easily, so I introduced a new goal: not having to change the code to alter presentation content, which is more convenient for people to are interested in the contents of the slide.

I didn't want to go all ham with a [php](http://php.net/) server, as I might not have the luxury to deploy it. I decided to look into a [javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript) solution to the problem.

## Building the solution

There is already a javascript function that to change the attribute of a element:

```javascript
document.getElementById("foo").setAttribute("data-markdown", "my.md" );
```

The only problem is that this function has to be called before the markdown plugin is called for the file to actually be processed. So this method could not be called on a event, but has to be called explictly during the document loading. In other words, we have to know what the file we wan't to load before the file is loaded. The simplest solution to this is to drectly store this information in the request url!

We could specify the change we want by a pseudo [HTTP GET method](http://www.w3schools.com/tags/ref_httpmethods.asp): altering the url to be something like:

```
https://my/url/index.html?date=2016-04-22
```

and extracting the results via javascript regex searching:

```javascript
var page_idx  = window.location.href.search(/date=[0-9]{4}-[0-9]{2}-[0-9]{2}/);
var page_file = window.location.href.substring(page_date_idx+5,page_date_idx+15);
```

This is of course assuming that the pseudo get method is in a fixed format. Then we could just past this string onto to the `setAttribute()` method. So if we include a small error prevention cycle, our `html` body would look something like:

```html
<div class="reveal">
   <div class="slides">
      <section id="__slide_main__"
         data-markdown="default.md"
         data-separator="^===$"
         data-separator-vertical="^---$">   <!-- Put your own seperators here -->
      </section>
   </div>
</div>
<script type="text/javascript">

   var page_date_idx = window.location.href.search(/date=[0-9]{4}-[0-9]{2}-[0-9]{2}/);
   if( page_date_idx > 0 ){
      var page_date_str  = window.location.href.substring(page_date_idx+5,page_date_idx+15);
      document.getElementById("__slide_main__").setAttribute("data-markdown", url_base + page_date_str+".md" );
   } else {
      document.getElementById("__slide_main__").setAttribute("data-markdown", url_base + "default.md" );
   }
</script>
```

Note that the name of the `id` is not arbitrary! The markdown plugin of reveal.js added it's own id tags to the generated elements, so if you don't have special characters in the id here, your navigation sequence might get messed up.


## Tidying up
Now, of course, could could start building the interface to make user experience event better, like a side-navigation bar to list all the availiable slides:

```html
<div class="side_bar">
   <h3>Availiable slides</h3>
   <ul>
      <li><a href="/base/url/index.html?date=2014-04-01">Slides for 2014-04-01</a></li>
      <li><a href="/base/url/index.html?date=2016-04-22">Slides for 2014-04-22</a></li>
      ...
      <li><a href="/base/url/index.html?date=2016-04-22">Slides for 2014-04-22</a></li>   
   </ul>
</div>
```

and css animations to pretty everything up. As to the automatic generation of this list, there is the problem that javascript is client-sides, and isn't always allow to access the filesystem on the server side[1](http://stackoverflow.com/questions/20822273/best-way-to-get-folder-and-file-list-in-javascript), and I'm still checking with our groups server to see what solution I might be able to use.

Since the slide I used for testing this framework contains some work sensitive content, I still haven't working version to github.

All in all, this is the first mini-project I have been done in javascript. I must say that anything that is graphics related in coding looks very gratifying, even is the results are still rather patchy :)
