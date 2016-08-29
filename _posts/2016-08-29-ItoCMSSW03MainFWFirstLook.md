---
layout: post
title: First look at the CMSSW framework
description: A first look at altering main function control flows with python files
tags: [c++, python, CMSSW]
modified: 2016-08-29
image:
  feature: code_head_1.png
  credit: ensc
---

In this article we are going to explore the (supposed) rational behind why the CMSSW framework is designed as it it, as well as getting a first look at how one could easily alter the contents of a EDM file using this framework. As before all, the concrete code used in this article could be found [here](https://drive.google.com/open?id=0Bw8_U9a0g9nHSG5rMEJWazdLMjQ)

## Hiding the Loop
By now, we might expect that our event-by-event calculations should have a skeleton code looking something like:
{%highlight c++%}
// open file

for( run.toBegin() ; !run.atEnd() ; run++ ){
   // come per run calculations...
   for( lumi.toBegin() ; !lumi.atEnd() ; ++lumi ){
      // some per lumi calculations
      for( event.toBegin() ; ! event.atEnd() ; ++event() ){
         // some per-event calculations...
         // some other per-event calculations...
         //...
      }
   }
}
// Save  calculations of files

{%endhighlight%}

Now we are going to try splitting the calculations to be handled by individual classes. One way of doing so would be using the concept of virtual functions:

{%highlight c++%}
class BaseCalculator {
   virtual void perrun_calc( run& )    {} ; // do nothing is not redefined by derived class
   virtual void perlumi_calc( lumi& )  {} ;
   virtual void perevent_calc( event& ){} ;
}

class MyCalc1 : public BaseCalculator {}; // implement one kind of calculation
class MyCalc2 : public BaseCalculator {}; // implement another kind of calculation

{%endhighlight%}

And the definition of the main functions would turn into something like this:
{%highlight c++%}
// open file

vector<BaseCalculator*> processList ;
processList.push_back( new MyCalc1 );
processList.push_back( new MyCalc2 );

for( run.toBegin() ; !run.atEnd() ; run++ ){
   for( auto& calc : processList ){
      calc->perrun_calc( run );
   }
   for( lumi.toBegin() ; !lumi.atEnd() ; ++lumi ){
      for( auto& calc : processList ){
         calc->perlumi_calc( lumi );
      }
      for( event.toBegin() ; ! event.atEnd() ; ++event() ){
         for( auto& calc : processList ){
            calc->perevent_calc( event );
         }
      }
   }
}

// Save  calculations of files

{%endhighlight%}

The merit of this is that when reading the files, we could seperate independent calculations from each other, so if we are only interested in altering one specific calculation, you could do so without having to comb through the entire high level control flow. Of course, there is the problem that calculations might dependent on the results of other user calculations. So how to define the control flow couldn't be as simple as above with a simple list of pointers. Luckily for use, the CMSSW framework as a set of very handy tools that could help us with the calculations.


## Looking at the calculator class : `EDProducer`s
Let us look at the extra structure require for the `EDProducer` class. It follows the standard `Package/Subpackage` structure required by `CMSSW`, but this time the C++ code is written in the `plugins` directory, and also an extra `python` directory.

```
Package
└── MyReader
    ├── plugins
    │   ├── BuildFile.xml
    │   └── MyReader.cc
    └── python
        └── ConfFile_cfg.py
```

Let us look at the `MyReader.cc` file first. It does the same as the `FWLite` example given in the previous article, dumping the contents of the "Gaussian" variable stored in a EDM file, except using the C++ technology described above:

{%highlight c++%}
#include <memory>
#include "FWCore/Framework/interface/Frameworkfwd.h"
#include "FWCore/Framework/interface/stream/EDProducer.h"
#include "FWCore/Framework/interface/Event.h"
#include "FWCore/Framework/interface/MakerMacros.h"

#include "FWCore/ParameterSet/interface/ParameterSet.h"
#include "FWCore/Utilities/interface/StreamID.h"

class MyReader : public edm::stream::EDProducer<>
{
public:
   explicit MyReader(const edm::ParameterSet&);
   ~MyReader();

   static void fillDescriptions(edm::ConfigurationDescriptions& descriptions);

private:
   virtual void beginStream(edm::StreamID) override;
   virtual void endStream() override;

   virtual void beginRun(edm::Run const&, edm::EventSetup const&) override;
   virtual void endRun(edm::Run const&, edm::EventSetup const&) override;
   virtual void beginLuminosityBlock(edm::LuminosityBlock const&, edm::EventSetup const&) override;
   virtual void endLuminosityBlock(edm::LuminosityBlock const&, edm::EventSetup const&) override;

   virtual void produce(edm::Event&, const edm::EventSetup&) override;

};
{%endhighlight%}

you could think of these private member functions corresponding to these execution points in the imaginary loop code:

{%highlight c++%}
// <--- beginStream() called here
for( run.toBegin() ; !run.atEnd() ; run++ ){
   // <-- beginRun() called here
   for( lumi.toBegin() ; !lumi.atEnd() ; ++lumi ){
      // <--- beginLuminosityBlock() called here
      for( event.toBegin() ; ! event.atEnd() ; ++event() ){
         // <--- produced() called here
      }
      // <--- endLuminosityBlock() called here
   }
   // <---  endRun() called here
}
// <--- endStream() called here

{%endhighlight%}

But note that the file opening and saving process are not handled by the `beginStream`/`endStream` functions. We will get to that in a bit. Now for the part of actually writing the "calculator" class


### Getting the handle.
Since `CMSSW` version 7.6.X, the `edm::EDGetToken` class is mandatory to be initialized during the calculator constructor, and this is used to get the `Handle` class we will be interested in.

{%highlight c++%}
class MyReader : public edm::stream::EDProducer<>
{
   ...
private:
   ...
   const edm::EDGetToken _mytoken;
};

MyReader::MyReader(const edm::ParameterSet& iConfig):
   _mytoken( consumes<double>( edm::InputTag("myprod","Gauss","Dummy")) )
{
}

void MyReader::produce(edm::Event& iEvent, const edm::EventSetup&)
{
   edm::Handle<double> myHandle;
   iEvent.getByToken( _mytoken, myHandle );
   std::cout << *myHandle << std::endl;
}
{%endhighlight%}
Take special note of the admittedly complex way of inserting the three magic strings. Also take note that the `Handle` and `Event` class are no longer using the one in the `fwlite` namespace, but the `edm` work space.

The other functions we are going to leave blank, since we are particularly interested a per-run or per-lumi calculation right now. There are a few book keeping functions we need to fill in, the `fillDescriptions` functions could filled as:

{%highlight c++%}
void MyReader::fillDescriptions(edm::ConfigurationDescriptions& descriptions) {
   //The following says we do not know what parameters are allowed so do no validation
   // Please change this to state exactly what you do use, even if it is no parameters
   edm::ParameterSetDescription desc;
   desc.setUnknown();
   descriptions.addDefault(desc);
}
{%endhighlight%}

and at the very end, add a magic MACROS function:

```
DEFINE_FWK_MODULE(MyReader);
```

Be sure that the argument in the MACROS functions is the same as the name of your class.

OK, now we have written our file, we are ready to compile! Let us look at the `BuildFile.xml` file for a bit:
{%highlight html%}
<use name="FWCore/Framework"/>
<use name="FWCore/PluginManager"/>
<use name="FWCore/ParameterSet"/>
<flags EDM_PLUGIN="1"/>
{%endhighlight%}

Again, it uses the same syntax to allow external libraries to be used. In our case, the minimal requirements for the "calculator" class are the three listed above. But the last line is new! This time, instead of saying we are compiling to a external library, we are making a "EDM plugin" (again with the name left to be default by the `="1"` flag). Now we are ready to compile!

```
scram b
```

## A python configuration file the "main" function
New we need to define the main functions. The `CMSSW` frameworks dynamically modifies a main function by reading a python file. Lets look at a minimal example: the file in directory `MyReader/python/ConfFile_cfg.py`:

{%highlight python%}
import FWCore.ParameterSet.Config as cms

# defining the process to run
process = cms.Process("MyProcess")

# How many events before early exit (-1 for effectively every event)
process.maxEvents = cms.untracked.PSet( input = cms.untracked.int32(-1) )

# Define input, note that for local files, the prefix 'file://' is required
# so the below is refering to the local file ./edm_example.root
process.source = cms.Source(
    "PoolSource",
    fileNames = cms.untracked.vstring(
        'file://edm_example.root'
    )
)

# Loading the producer, notice the string must the same as
# the argument you placed in the C++ MACROS function!
process.myProducer = cms.EDProducer(
    'MyReader'
)

# Defining sequence of producer instances you want to run.
process.p = cms.Path(process.myProducerLabel)

{%endhighlight%}

Then you could run the following command

```
cmsRun ConfFile_cfg.py
```

And you should get the output that looks something like:

```
29-Aug-2016 20:53:16 CST  Initiating request to open file file://edm_example.root
29-Aug-2016 20:53:17 CST  Successfully opened file file://edm_example.root
Begin processing the 1st record. Run 1, Event 383601, LumiSection 1919 at 29-Aug-2016 20:53:18.320 CST
2.9968
Begin processing the 2nd record. Run 1, Event 383602, LumiSection 1919 at 29-Aug-2016 20:53:18.321 CST
2.34539
...
Begin processing the 10th record. Run 1, Event 383610, LumiSection 1919 at 29-Aug-2016 20:53:18.322 CST
6.78134
29-Aug-2016 20:53:18 CST  Closed file file://edm_example.root

=============================================

MessageLogger Summary

 type     category        sev    module        subroutine        count    total
 ---- -------------------- -- ---------------- ----------------  -----    -----
    1 fileAction           -s file_close                             1        1
    2 fileAction           -s file_open                              2        2

 type    category    Examples: run/evt        run/evt          run/evt
 ---- -------------------- ---------------- ---------------- ----------------
    1 fileAction           PostEndRun                        
    2 fileAction           pre-events       pre-events       

Severity    # Occurrences   Total Occurrences
--------    -------------   -----------------
System                  3                   3

```

Congratulations! You have complete the event loop using the full CMSSW framework!

## Making the producer produce something
You might be suspecting from the name `EDProducer`, the this isn't just a calculator class. And you would be right! The `EDProducer` class could actually produce something into an output `EDM`. Suppose I want to compute the square of all the `Gauss` variables and store them in the event. We would need to alter the constructor:

{%highlight c++%}
MyProducer::MyProducer(const edm::ParameterSet& iConfig):
   _mytoken( consumes<double>(edm::InputTag("myprod","Gauss","Dummy")))
{
   produces<double>("GaussSquare");
}
{%endhighlight%}
which tell the main function that I want to produce a double collection, called `GaussSquare`. We of course have to alter the main `produce` function:

{%highlight c++%}
void MyProducer::produce(edm::Event& iEvent, const edm::EventSetup& iSetup)
{
   edm::Handle<double> myHandle;
   iEvent.getByToken( _mytoken, myHandle );

   std::auto_ptr<double> myptr( new double );
   *myptr = (*myHandle) * (*myHandle);
   iEvent.put( myptr, "GaussSquare" );
}
{%endhighlight%}

The three new lines creates a new pointer to a double, store the results that we want, and associate the double with the `GaussSquare` name. (If you are wondering what an `auto_ptr` is, it is essentially the same as the pointer, except it automatically deletes at the end of the scope it is declared in, the use of this STL class is manditory class).

Next we need to add the following lines to the python configuration file:

{%highlight python%}
# We wish t produce the files containing all the stuff produced in this process
process.out = cms.OutputModule("PoolOutputModule",
    fileName = cms.untracked.string('myoutput.root'),
)

# Marking the process as a EndPath (to be ran after everything else)
process.e = cms.EndPath( process.out )
{%endhighlight%}

Next you could run the command

```
cmsRun python/Conf1.py
```

You should see a new file called `myoutput.py`, and if you dump the contents of this file you should see:

```
Type                    Module                 Label           Process           
---------------------------------------------------------------------------------
double                  "myprod"               "Gauss"         "Dummy"           
int                     "myprod"               "Poisson"       "Dummy"           
double                  "myProducerInstance"   "GaussSquare"   "ProduceProcess"   
```

You now have a new collection! You could also being to see where the three magic strings are comming from! The module name, is the name you defined for the `cms.EDProducer` instance **in the python file** and the process name is the name you gave the process **in the python file**. Only the Label name is defined in the `C++` file. If you are only producing one variable of one type in a producer, you could even leave this label empty! This is important, as in the next section, we will be talking about how the python file could string multiple producers together, and see just how powerful using a python file to manage main function could be!

## Comparison between `FWLite` and the full framework
As you have probably seen, a CMSSW framework is aimed for event looping and event looping only. If you want to combine various `EDM` files with different weight associated with each file, it is doable, but very hard. The `CMSSW` full framework is meant to dealing with bulk calculations across a large number of files. The reason for using plugins and such C++ technologies is used so that the full framework could also multi-treaded across the grid with minimal amount of additional user coding. The full framework should be used to reduce the bulky, general purpose files into a handful of variable/objects that are interesting to an analysis, and then a more flexible `fwlite` framework could be used to read the output and produce the final calculation.
