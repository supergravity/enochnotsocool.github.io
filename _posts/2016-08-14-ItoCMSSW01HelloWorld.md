---
layout: post
title: Introduction to CMSSW Part I - The compiling environment and Hello world
description: Getting comfortable with writing and compiling code in CMSSW
tags: [c++, cmssw, analysis]
modified: 2016-08-14
image:
  feature: code_head_1.png
  credit: ensc
---


---

>> "It’s always best to start at the beginning." - Glinda the Good Witch, The Wizard of Oz

---

Let's begin with the simplest of examples for any programming language, how to write a hello world program. The example files used in this page could be found [here](https://drive.google.com/file/d/0Bw8_U9a0g9nHTHhjSlNiamQ5RWc/view?usp=sharing), and extract the file to the `CMSSW_X_X_X/src` directory with the command:

```
tar -zxvf HelloWorld.tar.gz
```
Remember each time you entry this directory, you should load the CMSSW environment with the command

```
cmsenv
```

If you wish to follow the build the sample up yourself, I will also be listing the directory structure used below

## Storing a main function and a first look at `BuildFiles`
I won't bore you with how you should write a hello world main function, but jump directly into the how you should store the file and how to run the compile command. You file should be store in the structure below in the `CMSSW_X_X_X/src` directory

```
CMSSW_X_X_X/src
└── HelloWorld
    └── Example1
        └── bin
            ├── BuildFile.xml
            └── <main>.cc

```

where your `<main>.cc` file could be named anything you want (with a correct extension), and contains you hello world main function. Next the `BuildFile.xml` must contain at least the line:

{% highlight html %}
<bin name="HelloWorld" file="main.cc"/>
{% endhighlight %}

next navigate to the `HelloWorld/Example1` directory and compile using the command

```
scram b
```

After the command has finished and if no errors has been produced, you should now have a new command available, type `HelloWorld` into your command line and see the result of your main file!

```
[user@machine Example1] HelloWorld
Hello World!
```

As you might have guessed, the `BuildFile.xml` replaces the `makefile` file to tell the compiler what to compile; in our case, the contents for our file `BuidlFile.xml` is rather simple: you could read as: request to build a `bin`ary file to be called `HelloWorld` using the main function found in the C++ file `main.cc`. The binaary file that is created would be stored under the `CMSSW_X_X_X/bin/`. The first thing that you could try out is that the you could write multiple main function files in the `Example1/bin` directory provided that the final executable are named differently! Just remember for each new `.cc` you write to add an entry in the `BuildFile.xml`.

It is also worth noting that `xml` is the fully named the [*extensible markup language*](https://en.wikipedia.org/wiki/XML), which is used for storing information in a document in a specific way so that is should be both human and machine readable. Though commonly seen in web application (alongside his famous cousin [`html`](https://en.wikipedia.org/wiki/HTML)), the usage of `xml` files are not limit to such applications only.

Writing a main function is only the first step. If you are the kind of guy that writes analysis code in a single file consisting of a few thousand lines of code, then I can guarantee you that after three weeks without looking at it, the code would become unintelligible, even by the writer. How you write you own libraries and split subroutines into multiple files is on big step for making you code readable. This ties in with the next part we will be looking at, about directories other than `bin` and how to write their corresponding `BuildFile.xml`

## The CMSSW BuildFile structure

Here we take a look at a more complicated structure

```
CMSSW_X_X_X/src
└── HelloWorld
    ├── Example2
    │   ├── bin
    │   │   ├── BuildFile.xml
    │   │   └── main.cc
    │   ├── interface
    │   │   └── funclib.hpp
    │   └── src
    │       ├── BuildFile.xml
    │       └── funclib.cc
    └── ExampleLib
        ├── interface
        │   └── MyClass.hpp
        └── src
            ├── BuildFile.xml
            ├── MyClass.cc
            └── MyClass_Say.cc
```

Inside The directory `HelloWorld/ExampleLib` is a class declaration and implementation

{% highlight c++ %}
// in MyClass.hpp
#ifndef HELLOWORLD_MYCLASS
#define HELLOWORLD_MYCLASS
#include <string>
class MyClass {
public:
   MyClass( const std::string& );
   virtual ~MyClass ();
   void Say( const std::string& );
private:
   const std::string _name;
};
#endif /* end of include guard: HELLOWORLD_MYCLASS */

// in MyClass.cc
#include "HelloWorld/ExampleLib/interface/MyClass.hpp"
MyClass::MyClass( const std::string& x ) : _name(x) {}
MyClass::~MyClass(){}

// in MyClass_Say.cc
#include "HelloWorld/ExampleLib/interface/MyClass.hpp"
#include <iostream>
void MyClass::Say( const std::string& x ){
   std::cout << this->_name << " says: " << x << std::endl ;
}
{% endhighlight %}

Here, take special note of how the c++ files includes a header. In the CMSSW compile environment, a new include path is added, which is you `CMSSW_X_X_X/src` directory! Now lets look at the new `BuildFile.xml`

{%highlight html%}
<export><lib name="1"/></export>
{%endhighlight%}

This file tell the compiler to make **every** c++ file in the src directory into a single shared object file, with the the library name left automatic. Now we look at how we use this shared object file.

In the directory `HelloWorld/Example2`, we could see in the `interface/funclib.hpp` and `src/funclib.cc` file that this "library" provides one function

{%highlight c++%}
#include "HelloWorld/ExampleLib/interface/MyClass.hpp"
#include <iostream>
int myfunction()
{
   static MyClass myinst("hello"); myinst.Say("World");
   std::cout <<  "Hello World from my function!" << std::endl;
   return 0;
}
{%endhighlight%}

Again, note how the external header file is included, Now for the building of this library we need to link it with the functions found in `HelloWorld/ExampleLib`, so we need to write in the file like:
{%highlight html%}
<use name="HelloWorld/ExampleLib"/>
<export><lib name="1"/></export>
{%endhighlight%}
Which is how we use libraries in CMSSW if the `export/lib` tag has been left with `name=1`! The last line of course, is so that we could export this library for external use. Now for the `bin` `BuildFIle.xml` where the main function file requires the use of both of these external libraries, the `BuildFile.xml` there would read something like:
{%highlight html%}
<use name="HelloWorld/ExampleLib"/>
<use name="HelloWorld/Example2"/>
<bin name="HelloWorld2" file="main.cc"/>
{%endhighlight%}

It is important to note here that the compile environment requires the `BuildFile`s and the corresponding directories to strictly follow `CMSSW_X_X_X/src/Package/Subpackage/<dir>`, where `<dir>` could only be named as a directory that the compile environment recognizes. But note that when you first initiate your `CMSSW` environment, though the `src` directory looks empty, the whole arsenal of packages and subpackages in the official `CMSSW` repository would be pre-compiled and at your disposal ( for a list of all the packages you could use, see the [official github page](https://github.com/cms-sw/cmssw))!  So the lines of code that read (for example):

{%highlight c++%}
// In a c++ file
#include "DataFormats/FWLite/interace/Event.hpp"
{%endhighlight%}
and in the build file
{%highlight html%}
<!--- In a BuildFile.xml  --->
<use name="DataFormats/FWLite"/>
{%endhighlight%}

would be totally legal.


## External libraries
What about packages outside of `CMSSW` that I want to use? Unfortunately, there is no easy way of including them. But there are special libraries that are commonly used in the field of experimental high energy physics that could be used without hassle. These libraries include

 * [ROOT](https://root.cern.ch/): `<use name="root"/>`
 * [ROOT](https://root.cern.ch/) plotting and [RooFit](https://root.cern.ch/roofit): `<use name="roofit"/>`
 * The majority of the [boost](http://www.boost.org/) library: `<use name="boost"/>`
 * The [boost program options](http://www.boost.org/doc/libs/1_61_0/doc/html/program_options.html) library: `<use name="boost_program_options"/>`
 * The [boost python](http://www.boost.org/doc/libs/1_61_0/libs/python/doc/html/index.html) library: `<use name="boost_python"/>`

## Defining additional compile flags

If you wish to define additional compilation flags (optimization, debugging, declaring MACROS... etc) then you could do so for individual `BuildFile.xml` with lines such as

{%highlight html%}
<flag CXXFLAGS="-O1 -g"/>
<flag CPPDEFINE="DEBUG=1"/>
{%endhighlight%}


## Additional documentations and closing words

This is by no means an exhaustive list of all the structures and options allowed in `CMSSW`, for the official documentation, see [here](https://twiki.cern.ch/twiki/bin/view/CMSPublic/SWGuideBuildFile). We will be picking the ones we need up as we go along, but hopefully, with this knowledge it will become more easy for you to organize the code in you analysis into a more organized structure.
