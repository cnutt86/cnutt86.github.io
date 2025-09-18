---
layout: post
title: Calculating Planet Volumes with NumPy
image: "/posts/numpy_planets.jpg"
tags: [Python, Numpy]
---

In this post, I present a mini-project employing NumPy to calculate planet volumes. There are two primary goals associated with this project. These goals include the following: 1) Calculating volumes for the planets in our solar system and 2) Calculating volumes for 1 million fictitious planets with randomly generated radii.

When carried out, this script demonstrates the speed and efficiency with which NumPy makes calculations.   

---

The first half of the script imports NumPy, creates a 1 dimensional array containing the radii of planets in our solar system, and works its way through the basic procedure for calculating the volume of sphere. This proceudre is then used to calculate volumes of the planets in our solar system using the radii contained in the NumPy radii array.  

```python
#create a numpy array with the radii of planets in our solar system
radii = np.array([2439.7, 6051.8, 6371, 3389.7, 69911, 58232, 25362, 24622])

# Test the the basic procedure for calculating volumes
r = 10
volume = 4/3 * np.pi * r**3
print(volume)

# Apply procedure to planet radii in the numpy radii array by replacing r with radii 
volumes = 4/3 * np.pi * radii**3
print(volumes)
```
The remainder of the script overwrites the radii array with a 1 dimensional array containing randomly generated radii for 1 million fictitious planets. Each radius in the array is somewhere between 1 and 1,000. The radii in the array are used to calculate volumes for all 1 million planets. 

```python
# Overwrite the radii array with a new array of 1 million randomly generated radii between 1 and 1,000
radii = np.random.randint(1, 1000, 1000000)

# With overwritten radii variable, run the same lines of code to calculate the volumnes for 1 million random planets
volumes = 4/3 * np.pi * radii**3
print(volumes)
```
