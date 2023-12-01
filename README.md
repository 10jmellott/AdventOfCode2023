# Advent of Code 2023

[Advent of Code](https://adventofcode.com/) is a celebration of coding mixed with the holiday spirit. In 2023 we are working on fixing a global snow crisis. When's the last time you've seen a white Christmas, eh?

> Something is wrong with global snow production, and you've been selected to take a look. The Elves have even given you a map; on it, they've used stars to mark the top fifty locations that are likely to be having problems.

> You've been doing this long enough to know that to restore snow operations, you need to check all fifty stars by December 25th.

## Shared

Commonly used imports throughout the application will make things a little easier moving forward


```python
from functools import reduce
import re
```


```python
# File Extensions
def readlinesext(f):
    return list(map(lambda l: l.strip(), f))
def readlinesnl(f):
    return list(map(lambda l: l.strip('\n'), f))
```


```python
# Data Read Functions
def openfile(name, testdata = False):
    dir = 'test' if testdata else 'data'
    return open(f'{dir}/{name}.txt', 'r')

```

# Day 1: Trebuchet?!

As mentioned in the premise, there's a problem with snow, so the elves decide the best way to solve this is to launch us into the sky with a Trebuchet. Somehow. However, while adjusting the trebuchet they found they can't read the calibration document as is was written by a more 'artsy' elf and need some help deciphering it.

Start by reading in the calibration document.


```python
f = openfile('01')
lines = readlinesext(f)
```

Now, for each line, it seems the only relevant part of the calibration are the first and last digits. We want to join each digit from each line as one number and then sum the value of each line. 


```python
sum = 0
for line in lines:
    # Capture the first numerical digit in line
    first = re.search(r'\d', line)
    # Reverse the line and capture the first numerical digit
    last = re.search(r'\d', line[::-1])
    # Condition necessary as test data changed between part 1 & 2
    if first and last:
        # Add the two captured digits together
        sum += int(f'{first.group()}{last.group()}')
sum

```




    55090



## Part 2

Something isn't right with just taking the first and last digits. Apparently some digits are spelled out in full (like 'nine'). So, now we need the first and last digits...but also need to consider possibilities like 'one' or 'five' as a digit. This certainly complicates things a bit.

To help us out I've built out a quick parsing function for the values


```python
def parseNumber(value):
    # Parse the spelled out digits to their numerical equivalent
    if value == 'one' or value == 'eno':
        value = 1
    elif value == 'two' or value == 'owt':
        value = 2
    elif value == 'three' or value == 'eerht':
        value = 3
    elif value == 'four' or value == 'ruof':
        value = 4
    elif value == 'five' or value == 'evif':
        value = 5
    elif value == 'six' or value == 'xis':
        value = 6
    elif value == 'seven' or value == 'neves':
        value = 7
    elif value == 'eight' or value == 'thgie':
        value = 8
    elif value == 'nine' or value == 'enin':
        value = 9
    return value
```


```python
sum = 0
for line in lines:
    # Capture the first numerical digit in line or the first match of a digit spelled out like 'one' or 'five'
    first = re.search(r'\d|one|two|three|four|five|six|seven|eight|nine', line)
    # Reverse the line and capture the first numerical digit or the first match of a digit spelled out in reverse like 'eno' or 'evif'
    last = re.search(r'\d|eno|owt|eerht|ruof|evif|xis|neves|thgie|enin', line[::-1])
    # Condition necessary as test data changed between part 1 & 2
    if first and last:
        # Add the two captured digits together
        sum += int(f'{parseNumber(first.group())}{parseNumber(last.group())}')
sum
```




    54845


