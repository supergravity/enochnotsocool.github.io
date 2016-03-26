---
layout: post
title: Using TLegend in with RooPlot
description: Mixing the different plotting machanics of pure Root and RooFit
tags: [root, roofit, plotting,]
modified: 2016-03-26
image:
  feature: thoughts.jpg
  credit: ensc
---

If you have used [Root](https://root.cern.ch/) and [RooFit](https://root.cern.ch/roofit), you will notice that the two system uses apparently different plotting interfaces. This poses some problems when you are attempting to plot RooFit objects together with Root objects. In particular, using [`TLegend`](https://root.cern.ch/doc/master/classTLegend.html) objects along side RooFit objects.

# Storing the plotted objects of Roofit
When plotting with RooFit, we typcially use something below:
{% highlight c++ %}
#include "RooFit.h"
#include "RooRealVar.h"
#include "RooDataSet.h"
#include "RooGaussian.h"
#include "TCanvas.h"
#include "RooPlot.h"
#include "TAxis.h"
using namespace RooFit ;

void rf101_basics()
{
  RooRealVar x("x","x",-10,10) ;
  RooRealVar mean("mean","mean of gaussian",1,-10,10) ;
  RooRealVar sigma("sigma","width of gaussian",1,0.1,10) ;
  RooGaussian gauss("gauss","gaussian PDF",x,mean,sigma) ;  

  RooPlot* xframe = x.frame(Title("Gaussian p.d.f.")) ;
  gauss.plotOn(xframe) ;
  sigma.setVal(3) ;
  gauss.plotOn(xframe,LineColor(kRed)) ;
}
{% endhighlight %}
Now we suppose we want to generate a [`TLegend`](https://root.cern.ch/doc/master/classTLegend.html) object to legend the objects that are being plotted.
I you are familiar with TLegend, this is done through the function:
```
tlegend_instance.AddEntry( <tobject_pointer> , "<Description_in_legend>" , "<plot_settings>")
```
The problem now is what are the `TObject` pointer that we need to pass over to the `TLegend` method? It cannot be the `gauss` instance in the code above, since it is used twice in the plotting function, so what is it?

## Getting the plotted objects
It turns out when you call the [`plotOn()`](https://root.cern.ch/doc/master/classRooAbsData.html#a41ef49f1f48f06ae9e5fd197c820aeb1) method, it generates a [`TGraph`](https://root.cern.ch/doc/master/classTGraph.html) object stored in the [`RooPlot`](https://root.cern.ch/doc/master/classRooPlot.html) instance. You coud actually get a list of all these objects by the simple snippet:
{% highlight c++ %}
for( int i = 0 ; i < xframe->numItems() ; ++i ){
   cout << xframe->getObject( i )->GetName();  
   // TObject* RooPlot::getObject(Int_t) const;
}
{% endhighlight %}
But there is a much better way of getting this thing, without having to guess which name corresponds to which object you have just plotted on the `RooPlot` object. By directly getting storing the object after you have called the `plotOn()` method:

{% hightlight c++ %}
gauss.plotOn(xframe);
TGraph* gauss_graph = (TGraph*)xframe->getObject( xframe->numItems() - 1  );
{% endhighlight %}

There is another merit of using this method. You could manually define the plot style using ROOT flavoured methods rather than using stuffing it all in the `plotOn()` methods call!

{% hightlight c++ %}
gauss_graph->SetFillStyle(3004);
gauss->SetLineColor( kGreen );
{% endhighlight %}

This gives you much more flexibility on the plots you are getting! Since not all [TGraphPainter](https://root.cern.ch/doc/master/classTGraphPainter.html) are not accessable with the RooFit `plotOn` [methods](https://root.cern.ch/doc/master/classRooAbsPdf.html#ae19cd5285edf475b744819b72d3ca517).

So now we could use the `TLegend` object as expected with this method!
{% hightlight c++ %}
TLegend l( 0.6, 0.6, 0.9, 0.9);
l.AddEntry( gauss_graph , "My Description" , "fl" );
l.Draw();
{% endhighlight %}

----

Hopefully this quick snippet has been useful!
