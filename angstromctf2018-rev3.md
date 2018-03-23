[home](https://adminadminctf.github.io/ctf/)

# Rev3
The ELF executable (rev3_32) we receive for this challenge takes a string as input, performs some operations on it and finally compares it to the following hardcoded string ```egzloxi|ixw]dkSe]dzSzccShejSi^3q```. If the two strings match, the program prints a success message.

Let's have a look at the ```encode``` function, which can be seen in the screenshot below:

![Imgur](https://i.imgur.com/BGS7Xzv.png)


What this function basically does is it takes each input character ```XOR 9``` and subtracts ```3``` from the result. In order to obtain the decoded string, we just use a small Python script which reverts the encoding.

```
encoded = "egzloxi|ixw]dkSe]dzSzccShejSi^3q" 
decoded = ""

for c in encoded:
  dc = ord(c) + 3
  dc =  dc ^ 9
  decoded += str(chr(dc))

print(decoded)
```

The decoded string is also our flag.

## actf{reversing_aint_too_bad_eh?}
