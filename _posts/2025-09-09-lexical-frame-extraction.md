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
The third output file is opened for reading and writing. The file is given the name attached to the variable 'newFile3'. The os.path.join function is used to include the third file in the folder attached to the 'new_folder'variable.

```python
fileOut3 = open(os.path.join(new_folder, newFile3),'w+')
fileOut3.write("Frame,Range,Raw Count,Norm Freq,Num Fillers,F1 Word,F1 Freq,TTR,Predictability,")
fileOut3.write("BIO Range,BIO Raw,BIO Nrmd,") #data categories for BIO sub-corpus
fileOut3.write("CIS Range,CIS Raw,CIS Nrmd,") #data categories for CIS sub-corpus
fileOut3.write("EHR Range,EHR Raw,EHR Nrmd,") #data categories for EHR sub-corpus
fileOut3.write("ENG Range,ENG Raw,ENG Nrmd,") #data categories for ENG sub-corpus
fileOut3.write("GEO Range,GEO Raw,GEO Nrmd,") #data categories for GEO sub-corpus
fileOut3.write("MPS Range,MPS Raw,MPS Nrmd,") #data categories for MPS sub-corpus
fileOut3.write("SBE Range,SBE Raw,SBE Nrmd\n") #data categories for SBE sub-corpus
```
**CONTINUE EDITING MARKUP HERE**
The fourth output file is opened for reading and writing. The file is given the name attached to the variable 'newFile4'. The os.path.join function is used to include the fourth file in the folder attached to the 'new_folder'variable.

```python
fileOut4 = open(os.path.join(new_folder, newFile4),'w+')
fileOut4.write("Frame,F1,F2,F3,F4,F5,F6,F7,F8,F9,F10,F11,F12,F13,F14,F15\n") #first row of csv is populated with frame and filler (F1,F2,etc.) column headers
```
An absolute file path is initialized for reading in the corpus. The glob.glob function with a for loop goes into the file path and employs various regular expressions to modify the each text file in the folder. Some modifications are made to the file names. These modifications become more relevant later on in the script. 

```python
path = r"/Users/christophernuttall/Dropbox/01 MacBook Pro Files/01 PhD ALT/01_Corpus Research/01_Copora/NSFAC_Untagged/*.txt"

for file in glob.glob(path):
    with open(file, encoding='utf-8-sig', errors='ignore') as file_in:
        file_name = re.sub("/.+/",'',file)
        sub_corp = re.sub("_.+", '', file_name) #replaces the end of the file_name string with nothing, leaving only the sub-corpus label
        text = file_in.read() #reads the file and saves the text to the 'text' variable
        text = text.lower()#makes all words in text lowercase.
        text = re.sub("<.+>\n",'',text) #ignores header information if present.
        text = re.sub("can't","can 'nt",text) #makes can't easier to work with
        text = re.sub("\d+", "#", text) # changes any number into # symbol
        text = re.sub("'s", "s'", text) #makes genitive case easier to work with
        text = re.sub("(n't|'d|'ll|'ve|'re|'m)",r" \1", text) #separates contracted words
        text = re.sub("([^\\w\\s\\'])",r" \1 ", text) #separates punctuation from words with some exceptions
        text = re.sub('-', '', text) #eliminates the hyphen between hyphenated words
        text = re.sub("\\s+", " ", text) #eliminates extra whitespace
        wordList = text.split() #splits text string into a list array of punctuation and words.
```
A for loop and a series of if-conditionals calculate word counts for the sub-corpora and the corpus as a whole.

```python

        # for loop to establish total word count and sub-corpora word counts.
        for word in wordList:
            if word not in puncList: # for every word not in the punclist, word count increases by 1 in corpus and sub-corpora
                totalWordCount += 1
                if sub_corp == 'BIO': #if word in BIO Sub-corp word count for that sub-corpus increases by 1
                    BIO_wcount += 1
                elif sub_corp == 'CIS': #if word in CIS Sub-corp word count for that sub-corpus increases by 1
                    CIS_wcount += 1
                elif sub_corp == 'EHR':#if word in EHR Sub-corp word count for that sub-corpus increases by 1
                    EHR_wcount += 1
                elif sub_corp == 'ENG':#if word in ENG Sub-corp word count for that sub-corpus increases by 1
                    ENG_wcount += 1
                elif sub_corp == 'GEO':#if word in GEO Sub-corp word count for that sub-corpus increases by 1
                    GEO_wcount += 1
                elif sub_corp == 'MPS':#if word in MPS Sub-corp word count for that sub-corpus increases by 1
                    MPS_wcount += 1
                else: #if word in SBE Sub-corp word count for that sub-corpus increases by 1
                    SBE_wcount += 1
```
**CONTINUE EDITING HERE**

```python
        # main for loop for finding all 4 word bundles, converting them to frames, and saving them to frame dictionary
        for i in range(len(wordList)-3):

            #four word bunde created and saved to 'fullBundle' variable.
            fullBundle = str(wordList[i]) + " " + str(wordList[i + 1]) + " " + str(wordList[i + 2]) + ' ' + str(wordList[i + 3])
            fullBundleList = fullBundle.split() # 4 word bundle is split into a list using split function
            check = any(item in puncList for item in fullBundleList) #any function compares bundle list with punclist to see if bundle list has punctuation
            if check == True: #if a bundle has punctuation in it, it is ignored.
                continue # command to head to the top of the for loop.

            #else: #if there is no punctuation, the bundle is saved to the 'frame134' or 'frame124 variable
            # the second or third word is replaced with '*'
            frame134 = str(wordList[i]) + " * " + str(wordList[i + 2]) + ' ' + str(wordList[i + 3])
            frame124 = str(wordList[i]) + " " + str(wordList[i + 1]) + ' * ' + str(wordList[i + 3])

            # if frame not in frame dictionary, the frame becomes a key in the dictionary attached to a list.
            # The list hols information for frame count, filename, file count, filler list 1, truncated filler list, sub corpora
            # counts and sets for holding file names for each sub-corpus, final set attached to key is used to
            # determine corpus range.
            if frame134 not in frame134Dic:
                # frame count, filename, file count, filler list 1, truncated filler list, frame counts and sets for sub-corporpora, set for sub-corp range
                frame134Dic[frame134] = [1, file_name, 1, [], [], 0, set(), 0, set(), 0, set(), 0, set(), 0, set(), 0, set(), 0, set(), set()]
            else:
                frame134Dic[frame134][0] += 1 # if frame in frame dic already, frame count is increased by 1.

                # if a new file is detected from the frame, file name is replaced, and file count increases.
                if file_name != frame134Dic[frame134][1]:
                    frame134Dic[frame134][1] = file_name
                    frame134Dic[frame134][2] += 1

            # removed filler word from bundle list is appended to filler list attached to key. List will be used to count total fillers eventually
            frame134Dic[frame134][3].append(fullBundleList[1])  # filler list with duplicates

            # removed filler added to second filler count list if it is not already in the list
            if fullBundleList[1] not in frame134Dic[frame134][4]:  # creating a filler list. This eliminates duplicates to facilitate TTR count
                frame134Dic[frame134][4].append(fullBundleList[1])

            #series of if/elif statemets work with subcorpora and various key values to determine raw counts in sub-corpus
            # and number of files in which frame occurs for the coprus and also corpus range.
            if sub_corp == 'BIO': #BI0 sub-corpus
                frame134Dic[frame134][5] += 1 #frame count increases by 1
                frame134Dic[frame134][6].add(file_name) #file name added to BIO set (later used for file count with len function)
                frame134Dic[frame134][19].add('BI0') #BIO added to final set attached to key (later used to determine sub corpus range with len function).
            elif sub_corp == 'CIS': # exact same thing as prag corpus, but with CIS sub-corpus
                frame134Dic[frame134][7] += 1
                frame134Dic[frame134][8].add(file_name)
                frame134Dic[frame134][19].add('CIS')
            elif sub_corp == 'EHR': #performing same functions as above, but with EHR sub corpus
                frame134Dic[frame134][9] += 1
                frame134Dic[frame134][10].add(file_name)
                frame134Dic[frame134][19].add('EHR')
            elif sub_corp == 'ENG': #performing same functions as above, but with ENG sub corpus
                frame134Dic[frame134][11] += 1
                frame134Dic[frame134][12].add(file_name)
                frame134Dic[frame134][19].add('ENG')
            elif sub_corp == 'GEO': #performing same functions as above, but with GEO sub corpus
                frame134Dic[frame134][13] += 1
                frame134Dic[frame134][14].add(file_name)
                frame134Dic[frame134][19].add('GEO')
            elif sub_corp == 'MPS': #performing same functions as above, but with MPS sub corpus
                frame134Dic[frame134][15] += 1
                frame134Dic[frame134][16].add(file_name)
                frame134Dic[frame134][19].add('MPS')
            else: #performing same functions as above, but with SBE sub corpus
                frame134Dic[frame134][17] += 1
                frame134Dic[frame134][18].add(file_name)
                frame134Dic[frame134][19].add('SBE')



            # if frame not in frame dictionary, the frame becomes a key in the dictionary attached to a list.
            # The list carries information for frame count, filename, file count, filler list 1, truncated filler list, sub corpora
            # counts and sets for holding file names for each sub-corpus, final set attached to key is used to
            # determine corpus range.
            if frame124 not in frame124Dic:
                frame124Dic[frame124] = [1, file_name, 1, [], [], 0, set(), 0, set(), 0, set(), 0, set(), 0, set(), 0, set(), 0, set(), set()]  # frame count, filename, file count, filler list 1, truncated filler list,
            else:
                frame124Dic[frame124][0] += 1 # if frame in frame dic already, frame count is increased by 1.

                # if a new file is detected from the frame, file name is replaced, and file count increases.
                if file_name != frame124Dic[frame124][1]:
                    frame124Dic[frame124][1] = file_name
                    frame124Dic[frame124][2] += 1

            # removed filler word from bundle list is appended to filler list attached to key. List will be used to count total fillers eventually
            frame124Dic[frame124][3].append(fullBundleList[2])  # filler list with duplicates

            # removed filler added to second filler count list if it is not already in the list
            if fullBundleList[2] not in frame124Dic[frame124][4]:  # creating a filler list. This eliminates duplicates to facilitate TTR count
                frame124Dic[frame124][4].append(fullBundleList[2])

            #series of if/elif statemets work with subcorpora and various key values to determine raw counts in sub-corpus
            # and number of files in which frame occurs for the corpus and also corpus range.
            if sub_corp == 'BIO': #BIO sub-corpus
                frame124Dic[frame124][5] += 1 #frame count increases by 1
                frame124Dic[frame124][6].add(file_name) #file name added to BIO set (later used for file count with len function)
                frame124Dic[frame124][19].add('BIO') #'BIO' added to final set attached to key (later used to determine sub corpus range with len function).
            elif sub_corp == 'CIS': # exact same thing as above, but with easp sub-corpus
                frame124Dic[frame124][7] += 1
                frame124Dic[frame124][8].add(file_name)
                frame124Dic[frame124][19].add('CIS')
            elif sub_corp == 'EHR': #performing same functions as above, but with call sub corpus
                frame124Dic[frame124][9] += 1
                frame124Dic[frame124][10].add(file_name)
                frame124Dic[frame124][19].add('EHR')
            elif sub_corp == 'ENG': #performing same functions as above, but with slaq sub corpus
                frame124Dic[frame124][11] += 1
                frame124Dic[frame124][12].add(file_name)
                frame124Dic[frame124][19].add('ENG')
            elif sub_corp == 'GEO': #performing same functions as above, but with slaq sub corpus
                frame124Dic[frame124][13] += 1
                frame124Dic[frame124][14].add(file_name)
                frame124Dic[frame124][19].add('GEO')
            elif sub_corp == 'MPS': #performing same functions as above, but with slaq sub corpus
                frame124Dic[frame124][15] += 1
                frame124Dic[frame124][16].add(file_name)
                frame124Dic[frame124][19].add('MPS')
            else: #performing same functions as above, but with asmt sub corpus
                frame124Dic[frame124][17] += 1
                frame124Dic[frame124][18].add(file_name)
                frame124Dic[frame124][19].add('SBE')





#for loop used to loop through 134 frame dictionary to perform various tasks with each frame
for i in sorted(frame134Dic):
    norm_freq = (frame134Dic[i][0]/totalWordCount)*1000000 #normed count for each frame is calculated for entire corpus .

    #sub-corpora norming counts
    BIO_norm = (frame134Dic[i][5]/BIO_wcount)*100000
    CIS_norm = (frame134Dic[i][7]/CIS_wcount)*100000
    EHR_norm = (frame134Dic[i][9]/EHR_wcount)*100000
    ENG_norm = (frame134Dic[i][11]/ENG_wcount)*100000
    GEO_norm = (frame134Dic[i][13]/GEO_wcount)*100000
    MPS_norm = (frame134Dic[i][15]/MPS_wcount)*100000
    SBE_norm = (frame134Dic[i][17]/SBE_wcount)*100000

    #cutoff set for data ouput. norm freq must be greater that 10, frame must appear in 5+ files, frame must appear in 3+ sub-corpora
    if norm_freq >= 10 and frame134Dic[i][2] >= 5 and len(frame134Dic[i][19]) >= 3:

        #first few cells under headers are added to output files: key, range, total raw caount, and total normed count
        fileOut.write(i + ',' + str(frame134Dic[i][2]) + ',' + str(frame134Dic[i][0]) + ',' + str(round(norm_freq, 2)) + ',')

        #predictability calculated fore each frame
        predictCount = Counter(frame134Dic[i][3])  # Counter functions used to count all fillers in expanded filler list.
        most_occur = predictCount.most_common(1) #most common function used to determine most frequently occurring filler. Creates tuple with filler and its count
        most_occur = most_occur[0] # filler saved to most_occur variable
        predictability = most_occur[1]/frame134Dic[i][0] #predictability calculated using most frequent filler count divided by raw count of frame.



        filler_TTR_count = 0  # figuring out TTR, also populating second CSV sheet with frame and associated fillers.
        fillers = frame134Dic[i][4]  # fillers is an array of all the unique fillers
        fileOut2.write(i + ',') #populating second CSV sheet with frame and associated fillers.

        # each filler is printed to the same row as it's frame using for loop. Also variant count is increased for each unique filler
        for filler in fillers:
            filler_TTR_count += 1 #the filler count increases by one for each new filler.
            fileOut2.write(filler + ',') #each filler is added to the row associated with its frame.
        fileOut2.write("\n") #once all fillers for a frame are added to row, a new row initates.
        TTR = filler_TTR_count/frame134Dic[i][0] #TTR count calculated using unique filler count divided by frame's raw frequency.

        #remaining data cells are populated fore each frame
        #In order of occurrence: Filler Count, F1 filler, F1 filler count, TTR, predictability
        fileOut.write(str(filler_TTR_count) + ',' + str(most_occur[0]) + ',' + str(most_occur[1]) + ',' + str(round(TTR, 2)) + ',' + str(round(predictability, 2)) + ",")

        #following lines add data for each sub-corpus to spreadsheet: range, raw frequency, normed frequency.
        fileOut.write(str(len(frame134Dic[i][6])) + ',' + str(frame134Dic[i][5]) + ',' + str(round(BIO_norm, 2)) + ",")
        fileOut.write(str(len(frame134Dic[i][8])) + ',' + str(frame134Dic[i][7]) + ',' + str(round(CIS_norm, 2)) + ",")
        fileOut.write(str(len(frame134Dic[i][10])) + ',' + str(frame134Dic[i][9]) + ',' + str(round(EHR_norm, 2)) + ",")
        fileOut.write(str(len(frame134Dic[i][12])) + ',' + str(frame134Dic[i][11]) + ',' + str(round(ENG_norm, 2)) + ",")
        fileOut.write(str(len(frame134Dic[i][14])) + ',' + str(frame134Dic[i][13]) + ',' + str(round(GEO_norm, 2)) + ",")
        fileOut.write(str(len(frame134Dic[i][16])) + ',' + str(frame134Dic[i][15]) + ',' + str(round(MPS_norm, 2)) + ",")
        fileOut.write(str(len(frame134Dic[i][18])) + ',' + str(frame134Dic[i][17]) + ',' + str(round(SBE_norm, 2)) + "\n")

#for loop used to loop through 124 frame dictionary to perform various tasks with each frame
for i in sorted(frame124Dic):
    norm_freq = (frame124Dic[i][0]/totalWordCount)*1000000 #normed count for each frame is calculated for entire corpus .

    #sub-corpora norming counts
    BIO_norm = (frame124Dic[i][5]/BIO_wcount)*100000
    CIS_norm = (frame124Dic[i][7]/CIS_wcount)*100000
    EHR_norm = (frame124Dic[i][9]/EHR_wcount)*100000
    ENG_norm = (frame124Dic[i][11]/ENG_wcount)*100000
    GEO_norm = (frame124Dic[i][13]/GEO_wcount)*100000
    MPS_norm = (frame124Dic[i][15]/MPS_wcount)*100000
    SBE_norm = (frame124Dic[i][17]/SBE_wcount)*100000

    #cutoff set for data ouput. norm freq must be greater that 10, frame must appear in 5+ files, frame must appear in 3+ sub-corpora
    if norm_freq >= 10 and frame124Dic[i][2] >= 5 and len(frame124Dic[i][19]) >= 3:

        #first few cells under headers are added to output files: key, range, total raw caount, and total normed count
        fileOut3.write(i + ',' + str(frame124Dic[i][2]) + ',' + str(frame124Dic[i][0]) + ',' + str(round(norm_freq, 2)) + ',')

        #predictability calculated fore each frame
        predictCount = Counter(frame124Dic[i][3])  # Counter functions used to count all fillers in expanded filler list.
        most_occur = predictCount.most_common(1) #most common function used to determine most frequently occurring filler. Creates tuple with filler and its count
        most_occur = most_occur[0] # filler saved to most_occur variable
        predictability = most_occur[1]/frame124Dic[i][0] #predictability calculated using most frequent filler count divided by raw count of frame.



        filler_TTR_count = 0  # figuring out TTR, also populating second CSV sheet with frame and associated fillers.
        fillers = frame124Dic[i][4]  # fillers is an array of all the unique fillers
        fileOut4.write(i + ',') #populating second CSV sheet with frame and associated fillers.

        # each filler is printed to the same row as it's frame using for loop. Also variant count is increased for each unique filler
        for filler in fillers:
            filler_TTR_count += 1 #the filler count increases by one for each new filler.
            fileOut4.write(filler + ',') #each filler is added to the row associated with its frame.
        fileOut4.write("\n") #once all fillers for a frame are added to row, a new row initates.
        TTR = filler_TTR_count/frame124Dic[i][0] #TTR count calculated using unique filler count divided by frame's raw frequency.

        #remaining data cells are populated fore each frame
        #In order of occurrence: Filler Count, F1 filler, F1 filler count, TTR, predictability
        fileOut3.write(str(filler_TTR_count) + ',' + str(most_occur[0]) + ',' + str(most_occur[1]) + ',' + str(round(TTR, 2)) + ',' + str(round(predictability, 2)) + ",")

        #following lines add data for each sub-corpus to spreadsheet: range, raw frequency, normed frequency.
        fileOut3.write(str(len(frame124Dic[i][6])) + ',' + str(frame124Dic[i][5]) + ',' + str(round(BIO_norm, 2)) + ",")
        fileOut3.write(str(len(frame124Dic[i][8])) + ',' + str(frame124Dic[i][7]) + ',' + str(round(CIS_norm, 2)) + ",")
        fileOut3.write(str(len(frame124Dic[i][10])) + ',' + str(frame124Dic[i][9]) + ',' + str(round(EHR_norm, 2)) + ",")
        fileOut3.write(str(len(frame124Dic[i][12])) + ',' + str(frame124Dic[i][11]) + ',' + str(round(ENG_norm, 2)) + ",")
        fileOut3.write(str(len(frame124Dic[i][14])) + ',' + str(frame124Dic[i][13]) + ',' + str(round(GEO_norm, 2)) + ",")
        fileOut3.write(str(len(frame124Dic[i][16])) + ',' + str(frame124Dic[i][15]) + ',' + str(round(MPS_norm, 2)) + ",")
        fileOut3.write(str(len(frame124Dic[i][18])) + ',' + str(frame124Dic[i][17]) + ',' + str(round(SBE_norm, 2)) + "\n")


#total word counts are printed for entire corpus and sub-corpora for my information and corpus documentation purposes.
print("The total word count is: " + str(totalWordCount))
print("BIO count: " + str(BIO_wcount))
print("CIS count: " + str(CIS_wcount))
print("EHR count: " + str(EHR_wcount))
print("ENG count: " + str(ENG_wcount))
print("GEO count: " + str(GEO_wcount))
print("MPS count: " + str(MPS_wcount))
print("SBE count: " + str(SBE_wcount))

#output files are closed
fileOut.close()
fileOut2.close()
fileOut3.close()
fileOut4.close()

#A little note to let me know everything is done.
print("Processing is complete. Check the output to make sure it meets expectations.")
```





