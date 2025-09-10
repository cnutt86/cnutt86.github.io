---
layout: post
title: Lexical Frame Extraction with Python
image: "/posts/Lexical_Frame.jpg"
tags: [NLP, Python]
---

This post contains the Python script I developed to extract all four-word lexical frames (e.g., the * of this, study findings * that, etc.)from a corpus of 3,500 NSF grant proposal abstracts. The script was also used to calculate key statistics reported in an academic article published in Applied Corpus Linguistics entitled "Profiling Lexical Frame Use in NSF Grant Proposal Abstracts."

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
integer variables used for calculatin various word counts are initatied. These include word counts for for the entire corpus in addition to word counts for each academic sub-discipline represented by the corpus.  

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
The primary dictionaries for storing lexical frames are initialized. The first dictionary stores frames containg a variant slot in the second position (the * of the). The second dictionary stores frames with a variant slot in the third position (study findings * that).

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
Four new csv file variables are initialized here. the output data will be saved to these files.

```python
newFile = "frame134_data.csv"
newFile2 = "frame134_fillers.csv"
newFile3 = "frame124_data.csv"
newFile4 = "frame124_fillers.csv"
```
The first output file is opened for reading and writing. The file is given the name attached to the variable 'newFile'. The os.path.join function is used to include the new file in the folder attached to the 'new_folder'variable.

```python
fileOut = open(os.path.join(new_folder, newFile),'w+')

#first few cells of CSV file populated with data categoroies
fileOut.write("Frame,Range,Raw Count,Norm Freq,Num Fillers,F1 Word,F1 Freq,TTR,Predictability,")
fileOut.write("BIO Range,BIO Raw,BIO Nrmd,") #data categories for BIO sub-corpus
fileOut.write("CIS Range,CIS Raw,CIS Nrmd,") #data categories for CIS sub-corpus
fileOut.write("EHR Range,EHR Raw,EHR Nrmd,") #data categories for EHR sub-corpus
fileOut.write("ENG Range,ENG Raw,ENG Nrmd,") #data categories for ENG sub-corpus
fileOut.write("GEO Range,GEO Raw,GEO Nrmd,") #data categories for GEO sub-corpus
fileOut.write("MPS Range,MPS Raw,MPS Nrmd,") #data categories for MPS sub-corpus
fileOut.write("SBE Range,SBE Raw,SBE Nrmd\n") #data categories for SBE sub-corpus
```
The second output file is opened for reading and writing. The file is given the name attached to the variable 'newFile2'. The os.path.join function is used to include the second file in the folder attached to the 'new_folder'variable.

```python
fileOut2 = open(os.path.join(new_folder, newFile2),'w+')

#first row of csv is populated with frame and filler (F1,F2,etc.) column headers
fileOut2.write("Frame,F1,F2,F3,F4,F5,F6,F7,F8,F9,F10,F11,F12,F13,F14,F15\n") #first row of csv is populated with frame and filler (F1,F2,etc.) column headers
```








