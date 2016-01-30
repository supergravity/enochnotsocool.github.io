---
layout: post
title: "Thoughts on Python"
description: "After coming back to python after two years, what is different"
tags: [thoughts, computing, python, c++]
image:
  feature: abstract-6.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

Growing up using C/C++, I first despised python when I first used it two years ago in the 
[*Numerical Methods and Programming*](https://www.facebook.com/NTU2013NMP/) course. 
I thought is was a messy, ambiguous, and generally unpleasant language to work with.
Over time, having also taken a more concrete courses in C++: the (in)famous 
[*Data Structure and Programming*](https://nol.ntu.edu.tw/nol/coursesearch/print_table.php?course_id=901%2031900&class=&dpt_code=9010&ser_no=81303&semester=104-1&lang=CH)
course from the EE department, and having nearly a years experience in writing code in the 
[CMS](http://cms.web.cern.ch/) Software environment, what has change in my mind about python?

## What I enjoyed about 
There are various aspects of python that I thoroughly:

### Extensive libraries/modules:
The Libraries provided by python are amazing: well documented, and straight forwards to use and enhance.
For those who have taken the *DSnP* course, I believe one of the most tedious and tasks was to write 
a command line interface to the manager classes in C++. Well in python, this is completely taken care of
in two python modules: [`Cmd`](https://docs.python.org/2/library/cmd.html) and [`argparse`](https://docs.python.org/2/howto/argparse.html)

In perspective of how much easier it is to use: My C++ *DSnP* final project had 600+ lines dedicated to just ten custom commands,
in the code use for my physics analysis, the python command line parser has ten custom commands in just  300+ lines, 
which also includes features that where not easy to implement in C++ such as varying input counts.

Using this comparison isn't entirely fair though. I know that the [C++ boost library](http://www.boost.org/) also
has extensive functions to help with the more tedious C/C++ tasks such as string parsing and system interactions.
But one key feature makes getting you hands on python so much more easier:

### Auto typing:
During a testing phase of a program, the absence of typing means that more time could be made out of what 
we actually want done rather than looking at all the gritty details of what variable should be what class
and whether said class could be initialized correctly.

Auto typing also makes it much simpler to design a uniform interface. If I wanted to update a options parser to 
get a list of input strings rather than getting a single string, I would have to go through the entire code
change the associated `std::string` instances to `std::vector<std::string>` and make sure everything compiles properly 
before we even begin to rethink the control flow.

In python, merely adding `nargs='+'` in the declaration of the `argparse` instance, and change the control flow accordingly.

This type of task is where the automatic typing of python really shines for me, where the expansion of your code could be 
performed very comprehensively and impacts very little of other parts of the code. 

### Method-string and class-string association:
I was asked one day, whether it is possible to declare a class by user inputs without the use of `if` statements.
In C++, I eventually concluded it was impossible, but in python this is totally possible. 
But what use is this feature? Well... it is very like that you might need to spawn class instances or class functions 
based on user input. And if you have many classes, C++ would require a very tedious wrapper function to check all of the registered classes,
a lot of work is required to be done by the user. Using this functionality of python, writing ''commands'' becomes much more easy 
and intuitive, where I could focus solely on the logic of the commands I am designing rather than worry about where 
the registration functions work as intended.

## What I miss about C++
Cool as python is, there is still a key aspect of C++ that I missed throughout my new experience of python: **Typing**,

It might seem strange that I should write so even though I enjoyed the auto typing. But it is a bit of a pain sometimes:
namely when two instances interact, wrong typing might cause the python program to crash, and to me, reading the crash message 
isn't always obvious as to what sort of issue might have caused the crash. 
In C++ programming, this sort of error is easily detectable by the compiler. 

Then there is the pass-by-reference/value issue. Maybe because I started with C++ and is now hard-wired into think with 
pass-by-value by default, but pass-by-reference to me is kind of scary when there isn't a marker to tell you that you might change
the value of a variable. In C++, the `&` and `const` key word makes the compiler take care of these sort of ''undesired features'',
aiding the programmer to ensure no such code appears in the final product; in python this work is solely left up to the programmer,
who is so much more prone to mistakes...



## C++/Python interfacing?
I had the idea, that we should leave the tasks that are more type sensitive ( the low level control flows ) to C++ code,
while the hight level control flow where nearly everything is simply strings and flags to python. And somehow interface
these two. The solution I am trying to adopt now is the 
[boost/python library](http://www.boost.org/doc/libs/1_60_0/libs/python/doc/html/index.html). 
This can help in compiling C++ code into python readable shared object files. But there are still many technicalities and questions

  * Compile time is very long for `boost/python` codes
  * Writing the `cmake` files makes things easier, but still an extra layer of maintenance.
  * Where should the cut between python and C++ be made?

I hope that I could eventually find a good compromise to the questions above in the future projects that I will be working with.






