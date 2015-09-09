---
layout: post
title: "NTUCMS Log - Sep 7th ~ Sep 13th"
description: "NTU CMS log"
tags: [NTUCMSLOG, bprimeKit, cms, computing]
modified: 2015-09-08 
image:
  feature: abstract-7.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

# Bprime Analysis Kit status update


## JSON file parser update
Included the new Golden JSON file [parsing tool](https://github.com/ntuhep/BprimeKit_Utils/blob/master/snippets/checkEvtTool.h) to GitHub,
also updated the twiki to include a [paragraph](https://twiki.cern.ch/twiki/bin/view/CMS/BprimeAnalysisKit#Working_with_Golden_JSON_files_w) 
on how to use it.

Alpha is a surprisingly C++ guy! The framework for the new checkEvtTool is now very satisfying!`



## `TriggerBooking.h` Update

### 2015RunC Update
Apparently Run C for this has begun! and new data is starting to come in. Interesting comment from Jack:

> As a researcher, you might be hoping for something to go wrong with the standard model. 
> But as a student, you will be hoping that nothing does.

### Ordering issue
In the previous version, I had been naively sorting all of the HLT names from all the input files. 
But, we could want to preserve the ordering of HLT from the previous production. So I had to add a filter to process only HLT names that wasn't already registered.

This is actually fairly simple, though I didn't go for the very efficient route, leading to a $$\mathcal{O}(n^2)$$ performance 
when it could have been $$\mathcal{O}(n \log n)$$, since we are dealing with sorted data. 
Due to the primitive arrays in sh script, I didn't wan't to waste time writing this.

The majority of the time was actually spend on trying to find a version of the HLT name list to revert to.
In the end had to engineer the name list file from the production version's `TriggerBoooking.h` File


> Keep backup of every file for every major event!



## Updating Ntuple retrieval script

#### The Problem:

A name used to identify the a dataset to CRAB is given in the format:

```
[sample label]/[run label]/[format]
```

After the processing the job, the generated file is file system:

```
[base dir]/[sample label]/[crab job name]/[submit time]/0000/[file name].root
```

This is not problem for the Monte Carlo samples, as the run label is usually not important. 
But with the **data** samples, there will be multiple runs with the same sample label.
This would mean that we would need to restore run label information in two places where we could make a difference:

1. The crab job name, 
2. The file name. This is not as desirable, since it would create a lot more junk file during the submission. 

This means that I have to write a new retrieval script for the for the data samples, and minor adjustments to the data submission script
Luckily, the data submission is only handled by Alpha, so there is little problem in the submission process.

What is stopping me from actually beginning the writting is the question: 
since there are so many functions we need to keep in sync for the submission and retrival process,
Would it actually be better to write an entirely new package for the process?
If the answer is yes, then there are more problems such as:

  * If I am to write a new package, should I stick the shell scripting? 
  * If that answer is no, which language should I be using the parse the files?
    Writing with C++ is annoying for tasks like this, and the types in python are driving me up the wall...

I hate problems like this...



## Ongoing process

* Verify the new Tstar MC data samples! It doesn't need to be good, just try and reconstruct the tstar peak to make sure there is nothing funny with the samples.
* Modify Jacky's so it could work for the new inputs!




