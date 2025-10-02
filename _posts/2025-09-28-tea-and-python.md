---
layout: post
title: Tea & Python
image: "/posts/coffee_python.jpg"
tags: [Python, Tea]
---

# My first project
## is all about
### how much
#### I LOVE
##### Python & Coffee!

---

I wanted to spend some time with you highlighting a few of my more recent projects. One nifty little quirk of code was developed to
find all of the prime numbers between 1 and a given integer input by a user, as well as output the largest. 
```
def prime_finder(n):

    number_range = set(range(2, n+1))
    primes_list = []
    while number_range:
        prime = number_range.pop()
        primes_list.append(prime)
        multiples = set(range(prime*2, n+1, prime))
        number_range.difference_update(multiples)
    print(primes_list)
    prime_count = len(primes_list)
    largest_prime = max(primes_list)
    print(f"There are {prime_count} prime numbers between 2 and {n}, and the largest is {largest_prime}.")

    
prime_finder(100)
```

I chose 100 for my number, but what if we choose 1 Million? The code works just as fast, easily returning the full set. 

```python
prime_finder(1000000) 
```

Let's get into what we are defining here in this chunk of code. Here we are establishing a Python function, which takes a users input which defines the upper limit of numbers we want to search through. Our lower limit is set as 2 (the smallest prime number) within the number_list variable, which establishes a set between these upper and lower limits. Next we establish a variable to store the primes list. Next, we set up a while loop that cycles through the number_list and adds each to the primes_list and creates a list of multiples of that number and then removes any of those multiples from the number_list using the difference_update function. Finally once every number in the number_list has been checked, the list is printed with a count of prime numbers in the resulting list and the max prime is listed. 

What are some ways I could make this better?

* I could include a check ensure the user input is greater than zero.
* I could include a check to ensure that the user input is even a number!
