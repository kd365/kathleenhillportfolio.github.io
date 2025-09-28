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
What are some ways I could make this better?

* I could include a check ensure the user input is greater than zero.
* I could include a check to ensure that the user input is even a number!
