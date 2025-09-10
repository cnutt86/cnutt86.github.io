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
fileOut.write("Frame,Range,Raw Count,Norm Freq,Num Fillers,F1 Word,F1 Freq,TTR,Predictability,")#first few cells of CSV file populated with data categoroies
fileOut.write("BIO Range,BIO Raw,BIO Nrmd,") #data categories for BIO sub-corpus
fileOut.write("CIS Range,CIS Raw,CIS Nrmd,") #data categories for CIS sub-corpus
fileOut.write("EHR Range,EHR Raw,EHR Nrmd,") #data categories for EHR sub-corpus
fileOut.write("ENG Range,ENG Raw,ENG Nrmd,") #data categories for ENG sub-corpus
fileOut.write("GEO Range,GEO Raw,GEO Nrmd,") #data categories for GEO sub-corpus
fileOut.write("MPS Range,MPS Raw,MPS Nrmd,") #data categories for MPS sub-corpus
fileOut.write("SBE Range,SBE Raw,SBE Nrmd\n") #data categories for SBE sub-corpus
```

Remember that when created a range the syntax is range(start, stop, step). For the starting point - we don't need our number as that has already been added as a prime, so let's start our range of multiples at 2 * our number as that is the first multiple, in our case, our number is 2 so the first multiple will be 4. If the number we were checking was 3 then the first multiple would be 6 - and so on.

For the stopping point of our range - we specify that we want our range to go up to 20, so we use n+1 to specify that we want 20 to be included.

Now, the **step** is key here.  We want multiples of our number, so we want to increment in steps *of our* number so we can put in **prime** here

Lets have a look at our list of multiples...

```ruby
print(multiples)
>>> {4, 6, 8, 10, 12, 14, 16, 18, 20}
```

The next part is the magic I spoke about earlier, we're using the special set functionality **difference_update** which removes any values from our number range that are multiples of the number we just checked. The reason we're doing this is because if a number is a multiple of anything other than 1 or itself then it is **not a prime number** and can remove it from the list to be checked.

Before we apply the **difference_update**, let's look at our two sets.

```ruby
print(number_range)
>>> {3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20}

print(multiples)
>>> {4, 6, 8, 10, 12, 14, 16, 18, 20}
```

**difference_update** works in a way that will update one set to only include the values that are *different* from those in a second set

To use this, we put our initial set and then apply the difference update with our multiples

```ruby
number_range.difference_update(multiples)
print(number_range)
>>> {3, 5, 7, 9, 11, 13, 15, 17, 19}
```

When we look at our number range now, all values that were also present in the multiples set have been removed as we *know* they were not primes

This is amazing!  We've made a massive reduction to the pool of numbers that need to be tested so this is really efficient. It also means the smallest number in our range *is a prime number* as we know nothing smaller than it divides into it...and this means we can run all that logic again from the top!

Whenever you can run sometime over and over again, a while loop is often a good solution.

Here is the code, with a while loop doing the hard work of updated the number list and extracting primes until the list is empty.

Let's run it for any primes below 1000...

```ruby
n = 1000

# number range to be checked
number_range = set(range(2, n+1))

# empty list to append discovered primes to
primes_list = []

# iterate until list is empty
while number_range:
    prime = number_range.pop()
    primes_list.append(prime)
    multiples = set(range(prime*2, n+1, prime))
    number_range.difference_update(multiples)
```

Let's print the primes_list to have a look at what we found!




