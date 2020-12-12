---
title:  "The 19 Challenge"
tags:
  - Math
  - Python
---

Matt Parker's recent [puzzle](https://www.think-maths.co.uk/19challenge) has had me intrigued.

<!--more-->

## The Challenge
"If you square all of the first 19 primes and add them you get a multiple of 19.
Enter any number n where the first n primes squared, add to a multiple of n. Any n ≠ 1 counts, but bigger is better!"

In other words, find the largest n where <a href="https://www.codecogs.com/eqnedit.php?latex=(\sum_{i=1}^{n}&space;p_i^2&space;)&space;%&space;n&space;=&space;0" target="_blank"><img src="https://latex.codecogs.com/gif.latex?(\sum_{i=1}^{n}&space;p_i^2&space;)&space;%&space;n&space;=&space;0" title="(\sum_{i=1}^{n} p_i^2 ) % n = 0" /></a> where p<sub>i</sub> is the ith prime.

## The Approach
While there are many different approaches to a problem like this, since this looks like a simple enough problem, I decided to use a simple tool: Python.

The iteration was a quick brute force.

#### First version:
run time of the first 10,000 primes: ~2:37 minutes

```python
import math
import sympy
from datetime import datetime
from tqdm import tqdm   # progress bar (may need to install package from: https://github.com/tqdm/tqdm)

start = datetime.now()

n_max = 10000
n_array = []
primes = [sympy.prime(p) for p in tpdm(range(1,n_max+1))]
primes_sq = [math.pow(p,2) for p in primes]

for n in tpdm(range(1,n_max)):
    if((sum(primes_sq[:n]) % n) == 0):
        n_array.append(n)

print()
print("Duration:",datetime.now()-start)
print(n_array)
```

But it wasn't until I ran it that I realize a couple of issues with my first iteration of this program.

The main issue is the generation of prime numbers via [sympy.prime()](https://docs.sympy.org/latest/modules/ntheory.html#sympy.ntheory.generate.prime)
which can take a while to generate all the primes of a given range; thus it doesn't scale well when generating on average 64-74 primes per second (at least on my computer).
If left generating primes for the full 6 days of the challenge it would only generate about 33,177,600 to 38,361,600 primes. I wasn't satisfied with that performance.

#### Latest version:
run time of the first 2 billion primes: ~48 minutes

I realize that it is likely that someone already has a database of primes, so why am I wasting compute resources and time generating primes?

I found a "[database](http://www.primos.mat.br/2T_en.html)" that has the first 2 billion primes (but also takes up ~20 GB).
I wrote an python script to download all 20 GB of primes:
```python
"""Primes downloader
Downloads 2 billion primes from http://www.primos.mat.br/2T_en.html for a total of 20 GB
"""

__author__ = "Chad Ross"
__email__ = "chadrt21@gmail.com"
__date__ = "2020-11-30"

import os
import py7zr
import requests
from tqdm import tqdm   # progress bar (may need to install package from: https://github.com/tqdm/tqdm)


# Download & unzip prime numbers files from: http://www.primos.mat.br/2T_en.html
# Each file contains 10 million primes (~101 MB) -> total of 2 billion primes (~20 GB)
num_files = 200
prime_files_URL = ['http://www.primos.mat.br/dados/2T_part{}.7z'.format(f) for f in range(1,num_files+1)]
prime_files = ['2T_part{}.7z'.format(f) for f in range(1,num_files+1)]

# Confirm download of 20 GB
print()
print("NOTE: Depending on internet speed this may take a up to a few hours.")
ask = input("Do you want to download 2 billion primes which would take up 20 GB (y/n):")
if (ask.upper() == 'Y'):

    # Download files
    print('Downloading files')
    for c in tqdm(range(0,len(prime_files))):
        file = requests.get(prime_files_URL[c], allow_redirects=True)
        open(prime_files[c],'wb').write(file.content)


    # Unzip files to current directory
    print('Unzipping files')
    for c in tqdm(range(0,len(prime_files))):
        py7zr.SevenZipFile(prime_files[c],mode='r').extractall()

    # remove zip files
    print('Removing zipped files')
    for c in tqdm(range(0,len(prime_files))):
        os.remove(prime_files[c])
```

After I have downloaded the 2 billion primes I refactored my code to go through those primes more efficiently.

It now takes about 14 seconds for each prime file which each contain 10 million primes;
thus take about 48 minutes to check the first 2 billion primes.
Much improvement from the first iteration of this program to do the same number of primes.


```python
import numpy
from tqdm import tqdm   # progress bar (may need to install package from: https://github.com/tqdm/tqdm)

# Download & unzip prime numbers files from: http://www.primos.mat.br/2T_en.html
# Each file contains 10 million primes (~101 MB) -> total of 2 billion primes (~20 GB)
num_files = 200
prime_files = ['2T_part{}.txt'.format(f) for f in range(1,num_files+1)]

n_array = []

file_count = 0
n_count = 1
for c in tqdm(range(1,len(prime_files))):

    primes = numpy.loadtxt(prime_files[file_count])
    primes = primes.flatten()

    primes_sq = numpy.power(primes,2)
    primes_cumsum = numpy.cumsum(primes_sq)

    for n in range(1,len(primes)):
        if((primes_cumsum[n-1] % n_count) == 0):
            n_array.append(n_count)
        n_count += 1

    file_count += 1

print()
print('n_array:',n_array)
print('len(n_array):',len(n_array))
print()
print('max n:',f'{n_array[-1]:,}')
numpy.savetxt('mpmp19_out.txt',n_array, fmt='%d',header='All n of the first {:,} primes'.format(n_count))

```

## Results
The program found 124 primes that meet <a href="https://www.codecogs.com/eqnedit.php?latex=(\sum_{i=1}^{n}&space;p_i^2&space;)&space;%&space;n&space;=&space;0" target="_blank"><img src="https://latex.codecogs.com/gif.latex?(\sum_{i=1}^{n}&space;p_i^2&space;)&space;%&space;n&space;=&space;0" title="(\sum_{i=1}^{n} p_i^2 ) % n = 0" /></a> in the first 2 billion primes:

```
# All n of the first 1,989,999,802 primes
1
19
37
455
509
575
20,597
65,440
77,492
232,688
608,672
801,215
903,876
1,411,584
1,581,030
1,693,696
1,753,552
1,777,664
1,781,184
1,830,912
1,881,344
2,455,062
2,511,706
2,863,408
3,076,132
4,169,664
5,844,992
6,099,712
6,209,536
6,368,064
6,648,320
8,388,608
12,058,624
16,777,216
17,719,296
19,378,816
20,473,343
20,971,520
21,306,518
21,757,952
22,151,168
26,515,392
28,565,504
32,758,064
33,554,432
37,545,984
41,943,040
48,041,728
49,283,072
49,383,296
50,331,648
50,495,755
54,418,880
56,555,520
67,108,864
67,371,008
77,514,452
81,687,936
86,607,580
88,381,184
93,143,031
94,489,900
95,475,376
100,663,296
126,159,136
134,217,728
137,887,744
142,653,848
142,706,176
150,994,944
153,918,104
159,383,552
164,222,656
167,772,160
179,137,536
190,143,680
201,326,592
238,640,512
239,337,472
243,752,960
260,046,848
261,062,656
268,435,456
318,115,328
324,194,892
329,252,864
351,285,706
355,730,432
369,098,752
375,390,208
385,875,968
402,653,184
404,291,584
435,995,802
443,678,720
455,294,464
469,762,048
498,581,504
503,696,192
536,870,912
584,581,120
612,368,384
621,356,544
625,655,808
631,039,494
671,088,640
696,254,464
713,226,040
771,751,936
883,556,352
940,413,696
1,028,653,056
1,031,798,784
1,073,741,824
1,090,519,040
1,160,210,560
1,198,641,152
1,249,902,592
1,374,642,176
1,400,865,537
1,440,913,408
1,559,150,592
1,856,852,720
1,962,934,272
```


I love simple problems like this which can show that programming is an iterative process.
