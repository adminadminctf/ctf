[home](https://adminadminctf.github.io/ctf/)

# Transposed - medium
In this challenge we get two files: a text file ```output``` containing the encrypted (or rather transformed) flag
```L{NTP#AGLCSF.#OAR4A#STOL11__}PYCCTO1N#RS.S``` and a piece of Python code ```encrypt.py``` which was used to transform the cleartext flag.

```
#-*- coding:utf-8 -*-

import random

W = 7
perm = range(W)
random.shuffle(perm)

msg = open("flag.txt").read().strip()
while len(msg) % (2*W):
    msg += "."

for i in xrange(100):
    msg = msg[1:] + msg[:1]
    msg = msg[0::2] + msg[1::2]
    msg = msg[1:] + msg[:1]
    res = ""
    for j in xrange(0, len(msg), W):
        for k in xrange(W):
            res += msg[j:j+W][perm[k]]
    msg = res
print msg
```
In hindsight this challenge was quite easy and it took me much longer than it should have. Most of the time was spent trying to wrap my head around inverting string operations, such as ```msg[0::2] ``` , which may be not too usual for everyone. So lets step through the code quickly.

## Analysis of encryption code

At first, a random permutation of the first seven positive integers (e.g. ```[5, 6, 1, 3, 2, 0, 4]```)  is created. Then the cleartext flag is read from a text file and padded with ```.``` such that the length of the padded flag is at least 14 and divisible by 7 (i.e. 14, 21, 28,...).

After padding the flag, a loop of 100 iterations is run through, carrying out the following steps:
1. Move the ```msg``` string's first character to the end of the string
2. Take every second character and and move it to the end of the string
3. Again, move the ```msg``` string's first character to the end of the string
4. The outer for-loop (index ```j```) iterates over the length of the ```msg``` string in increments of 7 (i.e. first iteration ```j = 0```, second iteration ```j = 7```, third iteration ```j = 21```,...). The inner for-loop  (index ```k```) then works on junks of 7 characters (starting at postion ```j```) by appendending the character at position ```j + k``` to the ```res``` string. Here the permutation comes into play, because the character appended to the ```res``` string depends on the value at position ```k``` in the permutation array. At the end, said ```res``` string will be the new ```msg``` string in the next iteration.

That's basically it: no key or any other shenanigans involved!

## The solution

In order to reconstruct the plaintext flag, the above code needs to be inverted (more or less :). As we don't know the actual permutation which was used during the encryption, we need to brute-force this part (there are 5040 possible possible combinations). To do this we put the actual decryption code into a loop which uses a different permutation during each iteration.
Now inverting the encryption code:
1. We start with the two nested for-loops (indices ```j``` and ```k```). There we simply remove the first character of the encrypted flag and place it at the position it initially had in the cleartext string. 
2. After the nested loops, the last character of the string is moved to position 0. 
3. Then the string is split in half and both halves are merged in an interleaving manner. For example, let ```s = "acegbdf"``` , left half ```u = "aceg"``` and right half ```l = "bdf"```. This would result in ```s = "abcdefg"``` after the merging. 
4. Then, the last character is moved to the beginning of the string. At the end of the iteration, ```res``` is assigned the value of ```msg``` in order to get transformed in the next iteration.

At the end of each of the 5040 iterations (which themselves consist of 100 transformation iterations), the output is tested to contain the string ```FLAG{```, in order to find the flag a little easier. (Thanks very much to the challenge author who decided to use a proper flag format!).

Here is my implementation of the solution. Im sure, that there are more efficient ways to solve this, but for the sake of comprehensibility of the target audience (and myself), I did not want to make it more complex (i.e. in terms of the string operations).

```
import random
from itertools import permutations

W = 7
perms = list(permutations(range(0, 7)))

for perm in perms:			# all permutations of range(0,6)
	perm = list(perm)
	msg2 = "L{NTP#AGLCSF.#OAR4A#STOL11__}PYCCTO1N#RS.S"
	res = msg2
	
	for i in xrange(100):
		msg = [''] * len(res)
		
		######## invert this the nested loops
		for j in xrange(0, len(msg), W):
			for k in xrange(W):
				msg[j+perm[k]] = res[0]
				res = res[1:len(res)]
		
		msg = ''.join(msg)
		
		####### invert this: msg = msg[1:] + msg[:1]
		msg = msg[len(msg)-1:len(msg)] + msg
		msg = msg[:-1]
		
		####### invert this: msg = msg[0::2] + msg[1::2]
		u = msg[0:int(len(msg)/2)] # first half
		l = msg[int(len(msg)/2):int(len(msg))] # second half
		r = [''] * (len(u) + len(l))
		r[::2] = u
		r[1::2] = l
		msg = ''.join(r)
		
		###### invert this: msg = msg[1:] + msg[:1]
		msg = msg[len(msg)-1:len(msg)] + msg
		msg = msg[:-1]

		res = msg

	if "FLAG{" in msg:
		print(msg)	
```

And here's the flag:
## FLAG{##CL4SS1CAL_CRYPTO_TRANSPOS1T1ON##}
