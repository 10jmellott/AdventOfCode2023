# Advent of Code 2023

[Advent of Code](https://adventofcode.com/) is a celebration of coding mixed with the holiday spirit. In 2023 we are working on fixing a global snow crisis. When's the last time you've seen a white Christmas, eh?

> Something is wrong with global snow production, and you've been selected to take a look. The Elves have even given you a map; on it, they've used stars to mark the top fifty locations that are likely to be having problems.

> You've been doing this long enough to know that to restore snow operations, you need to check all fifty stars by December 25th.

## Shared

Commonly used imports throughout the application will make things a little easier moving forward


```python
# Common Imports
from functools import reduce
import re
```


```python
# Basic Utility functions

def maplist(f, l):
    return list(map(f, l))
```


```python

# File Extensions
def readlinesext(f):
    return maplist(lambda l: l.strip(), f)
def readlinesnl(f):
    return  maplist(lambda l: l.strip('\n'), f)

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
value = 0
for line in lines:
    # Capture the first numerical digit in line
    first = re.search(r'\d', line)
    # Reverse the line and capture the first numerical digit
    last = re.search(r'\d', line[::-1])
    # Condition necessary as test data changed between part 1 & 2
    if first and last:
        # Add the two captured digits together
        value += int(f'{first.group()}{last.group()}')
value

```




    55090



**Part 2:** Something isn't right with just taking the first and last digits. Apparently some digits are spelled out in full (like 'nine'). So, now we need the first and last digits...but also need to consider possibilities like 'one' or 'five' as a digit. This certainly complicates things a bit.

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
total = 0
for line in lines:
    # Capture the first numerical digit in line or the first match of a digit spelled out like 'one' or 'five'
    first = re.search(r'\d|one|two|three|four|five|six|seven|eight|nine', line)
    # Reverse the line and capture the first numerical digit or the first match of a digit spelled out in reverse like 'eno' or 'evif'
    last = re.search(r'\d|eno|owt|eerht|ruof|evif|xis|neves|thgie|enin', line[::-1])
    # Condition necessary as test data changed between part 1 & 2
    if first and last:
        # Add the two captured digits together
        total += int(f'{parseNumber(first.group())}{parseNumber(last.group())}')
total
```




    54845



# Day 2: Cube Conundrum

The elves launch us into the sky and we land on 'Snow Island'. On the island you meet an elf who takes you on a walk to sit down and discuss the snow situation further. While we walk we play a game where the elf pulls cubes out of a bag to guess the number of cubes in the bag.

Start by recording the result of each game.


```python
# Open the file
f = openfile('02')
lines = readlinesext(f)

# Discard the game # as it's the same as the index
lines = maplist(lambda l: l.split(': ')[1], lines)

# Each line is single game in the format of 'Game #: # color, # color; # color; # color, # color, # color. Split each game into a set of cubes separated by ; and then split each color into a set of colors separated by ,
games = maplist(lambda l: maplist(lambda m: maplist(lambda n: n.split(' '), list(m.split(', '))), l.split('; ')), lines)
```

Now that we have the result of each game split out, the elf would first like to know which games would have been possible if the bag contained only 12 red cubes, 13 green cubes, and 14 blue cubes? In other words, which games contain a set with more than 12 red, 13 green, or 14 blue.


```python
limits = {
    'red': 12,
    'green': 13,
    'blue': 14
}

# For each game, check if the number of cubes is greater than the limit for that color
validsum = 0
for i in range(0, len(games)):
    game = games[i]
    gamevalid = True
    for set in game:
        for cubes in set:
            # Capture the color and number of cubes from cubes defined as '(#) (color)'
            num, color = cubes
            # Check if the number of cubes is greater than the limit for that color
            if int(num) > limits[color]:
                gamevalid = False
                break
    if gamevalid:
        validsum += i + 1

# Return the sum of game IDs that are valid
validsum
```




    2076



**Part 2:** We aren't getting snow as the Snow Island isn't getting any water. As we walk to the water source, we continue playing the game with a new question. What is the smallest number of cubes for the bag that would make each game possible. Or, in other words, what is the maximum number of each cube color in each game.


```python
gamemaximums = []
for game in games:
    maximums = { 'red': 0, 'green': 0, 'blue': 0 }
    for set in game:
        for cubes in set:
            # Capture the color and number of cubes from cubes defined as '(#) (color)'
            num, color  = cubes
            
            # Capture the maximum
            maximums[color] = max(maximums[color], int(num))
    gamemaximums.append(maximums)
```

Now we want the sum of the 'power' of each game, which is the maximum of each color from each game multiplies together.


```python
sum(map(lambda m: m['red'] * m['green'] * m['blue'], gamemaximums))
```




    70950



# Day 3: Gear Ratios

The cube game elf takes us to a gondola lift (is that a real thing?) where we meet another mechanic elf trying to fix the gondola lift. In order to fix it, we have to find a missing part. We start with the engine schematic to outline which parts go where and, thus, which one is missing.

Parts are broken down as numbers, separated by 'empty space' designated as a period (.). Any number adjacent to a symbol (non-numeric and non-period) is considered a 'valid part'. We want the sum of all valid part numbers. 


```python
f = openfile('03')
lines = readlinesext(f)

# Foreach line in lines, capture all number groups
part_numbers = maplist(lambda l: [(m.start(0), m.end(0)) for m in re.finditer(r'\d+', l)], lines)
# Capture each non '.' and non '#' characters from each line
# symbol_positions = maplist(lambda l: [(i, m.start(0)) for m in re.finditer(r'[^\.\d]', l)], lines)
```

Now, for each part number we need to outline the part to see if we find a symbol in the outline which would indicate the part number is currently valid. In this way we limit scope of parts the mechanic elf needs to check.


```python
def in_range(x, y):
    return not (x < 0 or y < 0 or y >= len(lines) or x >= len(lines[y]))

def is_symbol(x, y, gear = False):
    # Bounds checking
    if not in_range(x, y):
        return False
    # Check if position is gear
    if gear:
        return lines[y][x] == '*'
    
    # return True if the character at (x, y) in lines is not a '.' or r'\d'
    if lines[y][x] == '.':
        return False
    elif re.match(r'\d', lines[y][x]):
        return False
    return True

def is_valid_part(xstart, xend, y):
    # return True if any character in lines[y-1:y+1][xstart:xend] is_symbol
    for y in range(y - 1, y + 2):
        for x in range(xstart - 1, xend + 1):
            if is_symbol(x, y):
                return True
    return False
```

We need to sum up the value of all valid parts next.


```python
validsum = 0
for y in range(len(part_numbers)):
    for partcoords in part_numbers[y]:
        xstart, xend = partcoords
        validsum += int(lines[y][xstart:xend]) if is_valid_part(xstart, xend, y) else 0
validsum
```




    544664



**Part 2:** So, we found the missing part with that, but unfortunately there's another small problem. One of the gears is incorrect. A gear in this case is the '*' symbol. So, we need to run through all the parts again and pick out all the gears that are connecting exactly two parts. We can do this by looping through the same things as the first part, but marking down any connected gears for each part.


```python
gears = {}

def get_gears(xstart, xend, y):
    part_gears = []
    # return True if any character in lines[y-1:y+1][xstart:xend] is_symbol
    for y in range(y - 1, y + 2):
        for x in range(xstart - 1, xend + 1):
            if is_symbol(x, y, True):
                part_gears.append((x, y))
    return part_gears

for y in range(len(part_numbers)):
    for partcoords in part_numbers[y]:
        xstart, xend = partcoords
        part_gears = get_gears(xstart, xend, y)
        for gear in part_gears:
            if gear not in gears:
                gears[gear] = []
            gears[gear].append(int(lines[y][xstart:xend]))

ratio_sum = 0
for gear in gears:
    if len(gears[gear]) == 2:
        ratio_sum += gears[gear][0] * gears[gear][1]
ratio_sum
```




    84495585


