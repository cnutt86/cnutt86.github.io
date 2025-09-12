---
layout: post
title: Python Boxplots for Linguistic Analysis
image: "/posts/Dim2_boxplot.jpg"
tags: [Python, Data Visualization, Linguistic Analysis]
---

This post contains the Python script I developed to generate boxplots for visualizinng differences and similarities in the distribution of linguistic data associated with NSF grant proposal project summaries across four academic disciplines. Four sets of boxplots were generated to compare the use of linguistic features in the disciplines, with each set corresponding to one dimesion of linguistic variation previously established in a Principal Component Analysis (PCA). 

In this post, I do not draw any analytical inferences regarding the boxplots deriving from the program script. Instead, the primary purpose of this post is to highlight my ability to develop a Python script to produce a common form of data visualization. If your are interested in more in depth analytical discussion, you are more than welcome to request a copy of my dissertation study, *Perhaps It's a Matter of Discipline: An Analysis of Linguistic Variation in NSF Project Summaries*.   

---

Below the operating system, regular expressions, glob, and collections/counter modules are imported to access functions from those sources that will be used in the program

```python
import os
import re
import glob
from collections import Counter
```
A list variable is initialized and populated. It is used to facilitate word count and checking for punctuation in lexical bundles

```python
puncList = [',','.',':',';','[',']','"','?','(',')','-','--','%','$','@','!',"|","{","}","=",'+','<','>','/',"\\"]
```
integer variables used for calculating various word counts are initatied. These include word counts for the entire corpus in addition to word counts for each academic sub-discipline represented by the corpus.  

```python
totalWordCount = 0

BIO_wcount = 0
CIS_wcount = 0
EHR_wcount = 0
ENG_wcount = 0
GEO_wcount = 0
MPS_wcount = 0
SBE_wcount = 0
```
The primary dictionaries for storing lexical frames are initialized. The first dictionary stores frames containg a variant slot in the second position (*the * of the*). The second dictionary stores frames with a variant slot in the third position (*study findings * that*).

```python
frame134Dic = {}
frame124Dic = {}
```
The title of a new folder "NSFAC Frame Data" populates the new_folder variable. The if conditional checks to see that that file does not exist and creates a new folder with the os.makedirs function.

```python
new_folder = "NSFAC Frame Data"
if not os.path.exists(new_folder):
    os.makedirs(new_folder)
```
