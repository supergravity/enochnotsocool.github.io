---
layout: post
title: First Look at the EDM file format
description: Understanding how to read and analyze the contents of an EDM file
tags: [edm, c++, analysis]
modified: 2016-08-15
image:
  feature: code_head_1.png
  credit: ensc
---

In high energy physics, what we typically analysis is a list of events, extract what we deem useful from each event to compute physical variables like coupling strengths, production cross sections and such. But what variables in an event are deemed useful varies greatly from analysis to analysis. The Event Data Model format is a core backbone to the CMS software suite, giving great flexibility to the user to define an event by the variables they are interested in. Here we are first going to look at how you could look into the contents of a EDM file, looping through the events yourself.


## Seeing what is available in an file
The EDM files varies from user to user. There are official "tiers" that contain a general list of physical "objects" that annalists are typically interested. And there could be user-made EDM files, where the property of the event is condensed down to a single `double` variable of interested. We are going to provide a dummy EDM file [here](some link) to use as an example, and give instruction to find official EDM files that you should be able to get is you have access to a CMSSW environment.

As always, move to a `CMSSW/src` directory and load the environment by `cmsenv`. To look what is variables are available in each event, run the command
```
edmDumpEventContent  yourfile.root
```
You should get an output like

```
Type       Module     Label      Process   
-------------------------------------------
double     "myprod"   "myrand"   "Dummy"   
```
The first column is the C++ type needed to read the variable. The last three strings are related with how this variable was produced. You can think of it as three magic strings that allows you to acces the variable. This dummy file isn't very interesting. It just contains a single `double` variable. The outputs of a standard edm files is much more interesting:

```
Type                                  Module                      Label             Process   
----------------------------------------------------------------------------------------------
...
double                                "fixedGridRhoAll"           ""                "RECO"    
double                                "fixedGridRhoFastjetAll"    ""                "RECO"    
...
vector<pat::Electron>                 "slimmedElectrons"          ""                "PAT"     
...
vector<pat::Jet>                      "slimmedJets"               ""                "PAT"     
...
vector<pat::MET>                      "slimmedMETs"               ""                "PAT"     
...
vector<pat::Muon>                     "slimmedMuons"              ""                "PAT"     
...
unsigned int                          "bunchSpacingProducer"      ""                "PAT"
```
where you could see what look to be like physical objects.

To read this file with a main file you write yourself, you use the `fwlite::Event` and `fwlite::Handle` class as something like:
{%highlight c++%}
#include "DataFormats/FWLite/interface/Handle.h"
#include "DataFormats/FWLite/interface/Event.h"
#include "DataFormats/FWLite/interface/ChainEvent.h"
#include <iostream>
int main()
{
   fwlite::event ev( TFile::Open("myedmfile.root") ); // For single files
   // fwlite::ChainEvent ev ( {"file_1", "file_2", "file_3", ...} ); // For multiple files
   fwlite::Handle<double> mydouble;
   for( ev.toBegin() ; !ev.atEnd() ; ++ev ){
      mydouble.getByLabel( ev , "myprod",   "myrand",   "Dummy"   );
      std::cout << *mydouble << std::endl;
   }
}
{%endhighlight%}

You have to correct the `BuildFile` accordingly. The class `fwlite::Event` the class that helps with iterating through the file(s), and `fwlite::Handle` is the class that helps exposes the data in the corrected datatype. Essentially, on could treat a `Handle<T>` instance after the `getByLabel()` call to be a pointer to a `const T` instance.

There! That is all you need to know to perform analysis using your own main function!


## Comparison with ROOT TTree flavoured files
If you are familiar with `ROOT` [`TTree`](https://root.cern.ch/doc/master/classTTree.html) class, you might think this is a reinvention of the wheel. A simple root flavored class could be read as something like:
{% highlight c++ %}
TFile *myfile =  new TFile("myfile.root");
TTree *mytree = (TTree*) myfile->GetObject("mytree",mytree);
double mydouble;

mytree->SetBranchAddress("myvar",&myvar);
for( int i = 0 ; i < i < mytree->GetEntries() ; ++i ){
   mytree->GetEntry(i);
   cout << mydouble << endl;
}
{% endhighlight %}

In some sense, you are correct. I believe that the EDM file format is indeed powered by the `TTree` technology, but there are several advantages that could be achieved by using this wrapper system.

### Intuitive Modification
As we will see shortly, the EDM file is intuitive to manipulate. You could:

 * remove entries
 * add variable
 * remove variables
 * remove parts of arrays

that was stored in a large file to produce a new, slimmed and much smaller size file with very intuitive code writing.

In high every physics, un-selected data typically contain up to billions of events, most of which are not particularly interesting to the analysis, and even the events that are considered interesting might contain particles that are uninteresting. The ability to strip down these files to a manageable size is a key step in making analysis work of any kind possible at all. Yes there would be ways to do it using simple ROOT `TTree`, but why not use a system that has already been tested and designed by experts? It save you most of the pain of debugging, and focus more on the selection that you want to run.

### Ease of Encapsulation
As you can see when dumping the contents of an `EDM` file, you could see that entire classes and array of classes could actually be stored inside the file! To be honest, `TTree`s could save classes as well ( see [example](https://root.cern.ch/root/html/tutorials/tree/tree4.C.html]) ). But to do this in a unmodified root ( outside `CMSSW` ) is rather finicky. You have to generate class dictionary files, load the class C++ file every time you want to use the class. In the end it your root macros a mess. You could use a `makefile` to automate this process, but if you are willing to write a make file, why mot just use the `CMSSW` framework, where dictionary generation, `BuildFile` writting is so much simpler?

What's the use of encapsulation? If you have used the a `TTree` consisting only of C++ concrete types before, you might be familiar with this kind of code for getting the invariant mass of each jet pair in an event:

{%highlight c++%}

int size_of_jetlist;
double jetpt[SIZE];
double jeteata[SIZE];
...

tree->SetBranchAddress( "sizeofjet" , *size_of_jetlist );
tree->SetBranchAddress( "jetpt"     , jetpt );
...

for( i = 0 ; i < tree->GetEntries() ; ++i ){
   tree->GetEnty(i);
   for( j = 0 ; j < size_of_jetlist ; ++j ){
      for( k = 0 ; k < size_of_jetlist ; ++k ){
         TLorentzVector jet1 ; jet1.SetPtEtaPhiE( jetpt[j], jeteta[j] .... );
         TLorentzVector jet2 ; jet2.SetPtEtaPhiE( jetpt[k], jeteta[k] .... );
         TLorentzVector sum = jet1 + jet2;
         cout << sum.M() << endl;
      }
   }
}
{%endhighlight%}

There are several problems with writing code like this:

* Managing variables becomes repeated chore that has to be rewritten for every analysis.
* Counter-intuitive index handling: why is the `jetpt` variable capable of running a separate index with the `jeteta` variable?
* Excessive amounts of variables that are only used once.

All in all, it makes the code needlessly long, difficult to read and difficult to maintain and alter. With the power of encapsulation classes already existing the `CMSSW`, the power of the `EDM` file format and `C++11` standard I could write:

{%highlight c++%}
fwlite::Event ev("myfile.root");
fwlite::Handle<std::vector<pat::Jet>> jethandle;

for( ev.toBegin() ; !ev.atEnd() ; ++ev() ){
   jethandle.getByLabel( ev, "MyLabel" );
   for( const auto& jet1 : *jethandle ){
      for( const auto& jet2 : *jethandle ){
         cout << (jet1.p4() + jet2.p4()).M() << endl;
      }
   }
}
{%endhighlight%}

Which ( at least in my opinion ) is a lot easier to read and understand than the long winded code of a flat `TTree` format.


## Arguments against the `EDM` file format

In all analysis I have come across, the `EDM` file format is typically disfavored over the plain `TTree` file (commonly referred to as Ntuples). The reasons I will list here, and also include the reason why these arguments are becoming increasingly invalid.


  * **Forced ties to `CMSSW` environment**: This might be a problem when internet connections are still not as reliable as they are now. But now, logging onto a machine with the correct environments should not be a problem most of the time, and a uniform environment makes it easier to debug in most of the cases.

  * **The `EDM` file format is large**: It is true that the `EDM` file contains a much larger header space than a normal `TTree` file. But the main bulk of the `EDM` file actually comes in the physical object classes storing a lop of variables. Flattening these objects to arrays with variables dropped out could be used to reduce file size, but comes at the risk that wanted variable might be thrown out and there is an extra level of maintenance that needs to be worked on in addition to the analysis (ntuplizer codes are notoriously hard to maintain). I believe that one should focus on how to remove events and objects that are not useful to the analysis in question is a much more effective may of reduce storage space required for an analysis.

  *  **Flat `TTree`s are "intuitive" to work with**: This is an argument that really rubs me the wrong way. The variables themselves are easier to see in a `TBroswer`, but that relation of the variables with each other are not. To me, the argument of "flat arrays" being easier to work with then encapsulated objects stems form the horrible habit of writing code before thinking about that you are writing. You only want something to work now rather than work for a long period of time.

  * **The `EDM` file format is difficult to learn** This is perhaps the only valid argument in my opinion. Learning how to read and modify `EDM` files of your own is not easy. Learning ROOT is already difficult enough same reason of spare documentation (and extremely counter C++ intuition designs), learning `CMSSW` and `EDM` file manipulation is even harder. But all in all, I think it would be worth the time.


## Closing words

I have said that manipulating the contents of an `EDM` file is intuitive. This is where the backbone of `CMSSW` really shines, but is not easy to learn, as it:

 * Requires extensive knowledge of C++ syntax: (templates, virtual functions)
 * Lacks a direct file for a main function and you could only interact with a main function through a *python* file.

In a lot of the cases, I could not tell you how the `CMSSW` framework is doing what it is doing. But hopefully I could give you enough information to get started and get familiar with this strange and powerful framework. In the next part, I will start getting you familiar with looping thought `EDM` files in this `CMSSW` framework, knowing how to read to file, and next tell you how to modify it's contents.
