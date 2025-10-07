---
layout: post
title: Generating Height and Weight Scatterplot with Matplotlib
image: "/posts/scatter_thumbnail.jpg"
tags: [Matplotlib, Data Visualization, Python]
---

This post contains the Python script intended to demonstrate my ability to use Mathplotlib to create a scatterplot. More specifically, the genterated scatterplot plots height and weight data from a sample of male individuals. The scatterplot deriving from this script suggests a positive correlation between height and weight, with weight increasing as height increases. 

The script goes beyond simply generating a scatter plot for the height and weight data. It also inserts and provides labels for horizontal and vertical lines in the plot representing the medians for both height and weight. Additionally, I take steps to highlight one data point in the scatterplot. The ability to highlight specific data points could be useful in various contexts. 

---

Below pandas and matplotlib are imported to access functions from those sources that will be used in the program.

```python
import matplotlib.pyplot as plt
import pandas as pd
```
Three DataFrames are created using pandas. First, the 'body_data' DataFrame is created from the height and weight data included in the csv file "weights_and_heights.csv". The other two DataFrames are the 'male' and 'female' DataFrames. While the 'female' DataFrame contains the subset of height and weight data for all females in the 'body_data' DataFrame, the 'male' Data_Frame contains the subset of all 'male' height ant wieght data.

```python
body_data = pd.read_csv("weights_and_heights.csv")
male = body_data[body_data["Gender"] == "Male"]
female = body_data[body_data["Gender"] == "Female"]
```
To make the final scatterplot more manageable, a fourth DataFrame containing a smaller random sample of 200 rows is taken from the 'male' DataFrame. the parameter 'random_state = 42' is used to ensure consistency in data selection in any future scripts. This parameter will be referenced in those scripts to ensure that I collect the same 200 rows. 
```python
male_sample = male.sample(200, random_state = 42)
```
The following line of code creates the 'patient' variable that eventually allows me to highlight a specific data point in the scatterplot later.

```python
patient = male_sample.loc[[705]]
```
median and min statistics are calcuated for height and weight data in the 'male_sample' DataFrame. Not only are these statisics used to facilitate the best method for positioning various informative labels on the scatterplot, the medians are used as part of the text labels. 

```python

median_weight = male_sample["Weight"].median()
median_height = male_sample["Height"].median()
min_weight = male_sample["Weight"].min()
min_height = male_sample["Height"].min()
```
Below is the core of this Python script. The lines of code in this block create the scatterplot and specificy various parameters for the appearance and content of the plot. Hashed comments provide more details regarding what the lines of code accomplish. 
```python
# Set scatterplot style
plt.style.use('seaborn-v0_8-poster')

# create scatter plot
# Plot weight (x-axis) against height (y-axis)
# make the color of the data points blue (coler = blue)
# make the size of the data points 700 square points (I believe 36 square points is standard)
# Make the the data points partially transparent (alpha = 0.5)
plt.scatter(male_sample["Weight"], male_sample["Height"], color = "blue", s = 700, alpha = 0.5)

# Create a second scatter plot for a single data point to be highlighted. This is done second to overlay it over the first scatterplot 
plt.scatter(patient["Weight"], patient["Height"], color = "pink", s = 700, alpha = 1.0, edgecolor = "red", linewidths = 2)

# Add a title for the scatterplot. Also, add labels for the x and y axes.
plt.title("Weight vs. Height for Males")
plt.xlabel("Weight (lbs)")
plt.ylabel("Height (in)")

# Show the median weight on the plot as vertical dashed line
plt.axvline(x = median_weight, color = "black", linestyle = "--")

# show median height on plot as horizontal dashed line. 
plt.axhline(y = median_height, color = "black", linestyle = "--")
 
# Add annotation for the median weight vertical line
# xy = (median_weight,min_height)is the coordinate point on the plot that the annotation is "anchored" to, using the plot's data coordinates. In this case, the annotation is pointing to the point with x-coordinate median_weight and y-coordinate min_height
# xytext = (10, -10) specifies the position where the text itself will appear, relative to the xy point. It's an offset from the xy point in the textcoords coordinate system.
# textcoords = 'offset pixels'defines the coordinate system for xytext. By setting it to 'offset pixels', the (10, -10) tuple means the text will be placed 10 pixels to the right and 10 pixels down from the xy point.
plt.annotate(text = f"Median Weight ({round(median_weight)} lbs.)", 
             xy = (median_weight,min_height), 
             xytext = (10,-10), 
             textcoords = "offset pixels", 
             fontsize = 16)

# Add annotation for the median height horizondal line
plt.annotate(text = f"Median Height ({round(median_height)} in.)", 
             xy = (min_weight,median_height), 
             xytext = (-10,10), 
             textcoords = "offset pixels", 
             fontsize = 16)

# Add annotation for the highlighted data point, which in this case is I am calling a medical patient
# bbox dictionary configures the bounding box around the text.
# arrowprops dictionary defines the properties of the arrow connecting the text box to the xy point
plt.annotate(text = "Patient 705",
             xy = (patient["Weight"], patient["Height"]),
             xytext = (140,74),
             fontsize = 25,
             bbox = dict(boxstyle = "round",
                         fc = "salmon",
                         ec = "red"),
             arrowprops = dict(arrowstyle = "wedge,tail_width=1.",
                               fc = "salmon",
                               ec = "red",
                               patchA = None,
                               connectionstyle = "arc3,rad=-0.1"))

# Prevent labels, titles, and other plot elements from overlapping or being clipped by adjusting the layot.  
plt.tight_layout()

# Save figure as png file. 
plt.savefig(fname = "exported_plot.png")

# Shows a visul of the plot
plt.show()

```
**Here is the final scatterplot generated by the script.** 

![alt text](/img/posts/height_weight_scatterplot.jpg "Scatter Plot of Male Weight and Height Data")

Thank you for taking the time to read through this script. Please feel free to contact me with any questions you may have.



