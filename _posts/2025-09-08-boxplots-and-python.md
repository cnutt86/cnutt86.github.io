---
layout: post
title: Python Generated Boxplots for Linguistic Analysis
image: "/posts/Dim2_boxplot.jpg"
tags: [Python, Data Visualization, Linguistic Analysis]
---

In this post, I present the Python script I developed to generate boxplots for visualizinng differences and similarities in the distribution of linguistic data associated with NSF grant proposal project summaries across four academic disciplines. Four sets of boxplots were generated to compare the use of linguistic features in the disciplines, with each set corresponding to one dimesion of linguistic variation previously established in a Principal Component Analysis (PCA). 

In this post, I do not draw any analytical inferences regarding the boxplots deriving from the program script. Instead, the primary purpose of this post is to highlight my ability to develop a Python script to produce a common form of data visualization. If your are interested in more in depth analytical discussion, you are more than welcome to request a copy of my dissertation study, *Perhaps It's a Matter of Discipline: An Analysis of Linguistic Variation in NSF Project Summaries*.   

---

Below pandas, matplotlib, and seaborn are imported to access functions from those sources that will be used in the program

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```
Before any visualizations can be generated, the data required for creating the boxplopts must first be imported from a CSV file and saved to a DataFrame. The following line of code accomplishes this task using a relative path to the CSV file. 

```python
df = pd.read_csv("DimensionScores_BoxPlot.csv")
```
The next two lines of code generate a box plot using the seaborn and matplotlib libraries. Together, they create a visualization of the distribution of numeric values associated with the the use of linguistic features in the first dimension of linguistic variation previously established in a PCA. The visualization relies on a categorical variable (Directorate) to group the numerical data by NSF disciplinary directorate. 

```python
# Create the boxplot of Dim1 grouped by directorate
plt.figure(figsize=(16, 11))
sns.boxplot(x='Directorate', y='Dim1', data=df, color='aquamarine')
```
The following lines of code modify various visual details for the box plot figure. In addition to adding a titles and x/y labels, the code also modifies the orientation of the x-axis labels and adds horizontal grid lines. 

```python
# Improve the plot appearance
plt.title('Boxplot of Dim1 Grouped by NSF Disciplinary Directorate', fontsize=16)
plt.xlabel('Disciplinary Directorate', fontsize=14)
plt.ylabel('Dimension 1', fontsize=14)
plt.xticks(rotation=45, ha='right')
plt.grid(axis='y', linestyle='--', alpha=0.7)
```
In this block of code, I calucate key statistics (overall mean, median, and mode) for the numerical data in the Dimesnion 1 column of the DataFrame. I then write code to indlcude this information in a data annotation on the box plot. Hashed comments provide a few more details as to how this is accomplished. 

```python
# Calculate key statistics to be displayed in the corner of the box plot figure. 
stats_text = f"Overall Mean: {df['Dim1'].mean():.2f}\n"
stats_text += f"Overall Median: {df['Dim1'].median():.2f}\n"
stats_text += f"Overall Std Dev: {df['Dim1'].std():.2f}"

#Add the stats_text to the box plot at 2% over on the x-axis and 90% up on the y-axis.
# xycoords='axes fraction' ensures the added data annotation stays in same relative location regardless of plot size.
# bbox=dict creates a box around the data annotation addhering to the arguments inside the parentheses. 
plt.annotate(stats_text, xy=(0.02, 0.90), xycoords='axes fraction', 
                bbox=dict(boxstyle="round,pad=0.5", fc="white", alpha=0.8))
```
