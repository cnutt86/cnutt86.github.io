---
layout: post
title: Calculating Planet Volumes with Numpy
image: "/posts/numpy_planets.jpg"
tags: [Python, Numpy]
---

In this post, I present a mini-project employing NumPy to calculate planet volumes. There are two primary goals associated with this project. These goals include the following: 1) Calculate volumes for the planets in our solar system and 2) Calculate volumes for 1 million fictitious planets with randomly generated radii.

When carried out, this script demonstrates the speed and efficiency with which NumPy makes calculations.   

---

The first half of the script imports NumPy, creates a 1 dimensional array containing the radii of planets in our solar system, and works its way through the basic procedure for calculating the volume of sphere. This proceudre is than used to calculate volumes of the planets in our solar system using the radii contained in the NumPy radii array.  

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
