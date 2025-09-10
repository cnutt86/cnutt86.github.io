---
layout: post
title: Lexical Frame Extraction with Python
image: "/posts/Lexical_Frame.jpeg"
tags: [NLP, Python]
---

This post contains the Python script I developed to extract all four-word lexical frames (e.g., the * of this, study finding * that)from a corpus of 3,500 NSF proposal abstracts. The script was also used to calculate key statistics reported in an academic article published in Applied Corpus Linguistics.

---

First let's start by setting up a variable that will act as the upper limit of numbers we want to search through. We'll start with 20, so we're essentially wanting to find all prime numbers that exist that are equal to or smaller than 20

```ruby
n = 20
```

The smallest true Prime number is 2, so we want to start by creating a list of numbers than need checking so every integer between 2 and what we set above as the upper bound which in this case was 20. We use n+1 as the range logic is not inclusive of the upper limit we set there

Instead of using a list, we're going to use a set.  The reason for this is that sets have some special functions that will allow us to eliminate non-primes during our search.  You'll see what I mean soon...

```ruby
number_range = set(range(2, n+1))
```

Let's also create a place where we can store any primes we discover.  A list will be perfect for this job

```ruby
primes_list = []
```

We're going to end up using a while loop to iterate through our list and check for primes, but before we construct that I always it valuable to code up the logic and iterate manually first.  This means I can check that it is working correctly before I set it off to run through everything on it's own

So, we have our set of numbers (called number_range to check all integers between 2 and 20. Let's extract the first number from that set that we want to check as to whether it's a prime. When we check the value we're going to check if it is a prime...if it is, we're going to add it to our list called primes_list...if it isn't a prime we don't want to keep it

There is a method which will remove an element from a list or set and provide that value to us, and that method is called *pop*

```ruby
print(number_range)
>>> {2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20}
```
If we use pop, and assign this to the object called **prime** it will *pop* the first element from the set out of **number_range**, and into **prime**

```ruby
prime = number_range.pop()
print(prime)
>>> 2
print(number_range)
>>> {3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20}
```

Now, we know that the very first value in our range is going to be a prime...as there is nothing smaller than it so therefore nothing else could possible divide evenly into it.  As we know it's a prime, let's add it to our list of primes...

```ruby
primes_list.append(prime)
print(primes_list)
>>> [2]
```

Now we're going to do a special trick to check our remaining number_range for non-primes. For the prime number we just checked (in this first case it was the number 2) we want to generate all the multiples of that up to our upper range (in our case, 20).

We're going to again use a set rather than a list, because it allows us some special functionality that we'll use soon, which is the magic of this approach.

```ruby
multiples = set(range(prime*2, n+1, prime))
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




