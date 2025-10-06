---
layout: post
title: Generating Height and Weight Scatterplot with Matplotlib
image: "/posts/Lexical_Frame.jpg"
tags: [NLP, Python]
---

This post contains the Python script I developed to generate a scatterplot comparing height and weight variables from a sample of male individuals. The scatterplot deriving from this script suggests a positive correlation between height and weight, with weight increasing as height increases. 

The script goes beyond simply generating a scatter plot for the height and weight data. It also inserts and provides labels for horizontal and vertical lines in the plot representing the medians for both height and weight. Additionally, I take steps to highlight one data point in the scatterplot. The ability to highlight specific data points could be useful in various contexts. 

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

