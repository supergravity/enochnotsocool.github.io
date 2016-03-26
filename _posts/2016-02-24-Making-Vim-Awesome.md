---
layout: post
title: Making Vim Awesome
description: Tips and tricks for making Vim Awesome
tags: [vim, computing, tips]
modified: 2016-02-24
image:
  feature: thoughts.jpg
  credit: ensc
  creditlink:
---

I guess the best way to say goodbye to vim would be to give a nice list of the things that I found handy in vim. As always with customizable tools like this, there is not definitive answer to how to make vim awesome. I would advise you to look at some basic `vimrc` settings and browse through [vim awesome](http://vimawesome.com/) to find the things that makes your vim workflow work best for you!

# General recomendations
For those who are still getting familiar with vim, the [interfactive vim tutorial](http://www.openvim.com/) is a good place to get started with the basic commands.

  * For general recommendation on the vimrc file, checkout the [vim wiki page](http://vim.wikia.com/wiki/Example_vimrc).
  * For a list of commonly used plugins, see [vim awesome](http://vimawesome.com/).
  * For in depth tutorials on popular vim plugins and vim scripting, check out [Vim cast](http://vimcasts.org/)


# Configuring Vim
As my brother said:

> Vim is not just a program, it is a standard to which people compare what one could expect out of their text editors

So vim is not the "Real men use h-j-k-l" elitist BS that some people like to say, but what is possible in the text editor: Custom key-mapping, macros, block editing, keystroke recording... etc. The true power of vim is not in what you get directly out of the box, but what is potentially possible by the vim configuration settings via the `~/.vimrc` file. In addition to the powerful engines aready given to you by vim (regex, syntax/filetype awareness...etc ), the configuration is programable, so that you could shape vim to suit your own specific needs.

----

## My `vimrc` settings
Here is the list of options that I have activated to improve the overall typing experience. If you are highly into text based stuff, I highly recomment checking out some of the options (and related) below to find what works the best for you!

### Indention settings
Indention is the simplest way of keeping maintaining appearance in text files, so might as well life easier

```
set tabstop=3       "" Setting indention width
set shiftwidth=3
set expandtab       "" Replacing tab with spaces
set smarttab        "" Use tab instead of spaces
set autoindent      "" Indenting to current indention levle on new line input
```

### Wild card settings
Finding files should be left to the computer, not the user. Open files with command line like auto complete

```
set wildmenu                "" Enable wild card
set wildmode=longest,full   "" Set to autocomplete to the longest prefix
set wildignore+=*.a,*.o     "" Setting to ignore non-textbased extensions
set wildignore+=*.bmp,*.gif,*.ico,*.jpg,*.png
set wildignore+=.DS_Store,.git,.hg,.svn
set wildignore+=*~,*.swp,*.tmp
```

### Search settings
Settings I found when I just into vim, molded my workflow around them...

```
set showmatch   "" Automatically move cursor to next results position
set hlsearch    "" Highlight search results
set incsearch   
set ignorecase  "" Ignore upper/lower-case different when searching all under-case
set smartcase   "" Case-dependent search when search query includes uppercase or regex
```

### Line number
Let vim display the line number.

```
set number            "" Display linenumber
set relativenumber    "" Show linenumber relative tu cursor
set numberwidth=4     "" Width of numberline column
```

### Miscellenious settings
Other settings

```
set showcmd                   "" Show input commands at bottom of screen
set backspace=2               "" Allow backspace across new-line
set formatoptions=c,q,r,t
set ruler                     "" Display cursor position
set background=dark           "" Choose color scheme for dark background
set mouse=a                   "" Enable mouse interaction
set shell=zsh\ --login        "" Set shell command environment to match regular terminal
```

Note that syntax highlighting would require `filetype on`, but we need to set up the plugin manager first.

----

## The Essential Plugins
Though I say that vim configuration, I haven't actually written any vim scripts myself, you could find a nice tutorial [here](http://learnvimscriptthehardway.stevelosh.com/) though. What I do use are the plugins that are already written by others and do minor settings to make it look better. For a nice list of availiable and actively maintained plugins, check out [vim awesome](vimawesome.org).

The following plugins listed below is what I consider to be essential in my workflow, and editing in vim simply wouldn't be the same without.


### Vundle - the package manager
A pacakge manager that is simple to use and maintain. To use it add the following lines to vimrc

```
set nocompatible
filetype off
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

Plugin 'gmarik/Vundle.vim'
""" Add pacakges like [github_user]/[repository name]
call vundle#end()
filetype on
filetype plugin on
filetype indent on
```

The lines that needs to be added to vim could be directly found on [vimawesome](https://vimawesome.org).

### Vim Airline - the informative interface
During editing, you might want to get some useful information about your editing conditions, like your operatin mode, the path of the file you are editing, the tabs you have open, the git branch, the cursor position... etc.
<figure>
   <img src="http://gdriv.es/ensc_github/vim_airline.png"/>
   <figcaption>A nice interface display!</figcaption>
</figure>

While all of this information is availiable by the vim command line, this package makes is possible to choose the information you are typically interested and display it in a consistence and pretty way!

To use add `Plugin 'bling/air-line'` to the plugin lines, for the better fonts, you might want to install the [powerline fonts](powerline.readthedocs.org/en/master/installation/linux.html) to you fonts (system or otherwise).

### Ultisnip - Programmable snippet insertion
In coding there are typically partterns that will occur over and over again (Class declaration, tags, loops and ifs, comment blocks...) Snippets allow you to add inputs by simple inputs and string substitution so that you don't need to key class names mulitple times. What's more, the output could be programable by python tu change based on you input in various sections!

<figure>
   <img src="https://camo.githubusercontent.com/296aecf30e1607233814196db6bd3f5f47e70c73/68747470733a2f2f7261772e6769746875622e636f6d2f5369725665722f756c7469736e6970732f6d61737465722f646f632f64656d6f2e676966"/>
   <figcaption>Ultisnip in action!</figcaption>
</figure>
To use add `Plugin 'SirVer/ultisnips'` to the plugins lines. See the github page Readme.md for examples on programmable snippets!

My little test script was to make a seperator comment what is easy to type! (For on how this is acheived, see my [github repository](https://github.com/enochnotsocool/UnixConfig/blob/master/vim/UltiSnips/cpp.snippets))

```
//----- This is a seperator -------------------------------------------
dummy_code = a ;

//----- This is also seperator, with more test ------------------------
dummy_code = b ;
```

This is the feature that I miss the most about vim when migrating to [atom](https://atom.io), The snippet system in atom simply isn't mature enough, and In my nearly two years in vim I have accumulated some snippets that is a bit annoing to port into atom's `cson` format.

----

## The Nice plugins
The following are packages that ones that are not commonly used in everyday workflow, But is nice to have around.

### Tabular - Aligning on characters
In the code I'm helping to maintain, there are usually long sections of declaration and assignment, and it helps with my sanity if I could align my code by the `=` operator or other aspects strings in the code!

Add the line `Plugin 'godlygeek/tabular'` to use this plugin, and check out the [vim cast video](http://vimcasts.org/episodes/aligning-text-with-tabular-vim/) to see how to use it!


### NerdTree - Displaying the directory structure
Since vim is command line based and a lot of the work flow would be based around paths, it would be nice that the usre could get a display of the directory sturcture if ever needed to be referenced. Since my discovery of the `tree` command in linux, this task is typically managed via command line. But I do like my a interface open when I work.


### YouCompleteMe - Advance auto completion system
This is a syntax aware, content aware, fuzzy matching auto completion system. This plugin is a bit hard to recommend: it's heavy, sluggish with large files, and has a lot of dependencies if you want it to work with self-defined C++ functions. but it works well with a number of other plugins (Ultisnip via supertab), and simply works with most of the suff I expect from a auto-completions system.

---

Well, that is that. Since vim will survive in my main program as a system file editor, most of the heavier plugins will be disabled (ultisnip and YouCompleteMe). If you want to use vim a you primary text editor for general purpose text related tasks, I highly recommend most of the settings that I have used above!
