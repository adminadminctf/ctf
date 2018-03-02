[home](https://adminadminctf.github.io/ctf/)

# Hashbrowns
For this challenge we're given a 32-bit ELF executable which, upon execution, prompts us to enter a password (up to 20 characters in length) and then outputs a string which seems to be some kind of hash, although of unkown sort. Additionally we learn, that the entered password is wrong/right.

Let's have a look at the hashbrowns in radare2. There are a lot of function calls (not on the picture) which are mostly IO - so nothing really special. The push ```str.8819d19069fae6b4bac183d1f16553abab16b54f``` in ```main```  is where it gets interesting. Looks like a hash! As we can see in the screenshot below, there is some kind of comparison, presumably of our hashed input and the stored hash, which then results in the path our program takes (```success``` vs ```invalid_password```).

![Imgur](https://i.imgur.com/UrWD8S1.png)

As this challenge is only worth 100 points, we do not need to worry about the internals, which are not that easy to examine anyway, because the dynamic linking. So we concentrate on the hash we found. There are two options: first, cracking it using hashcat in brute-force or dictionary mode. Second, just lazily copy+paste it into our rainbow table service of choice and let them do the magic. Et voilà, it actually is the SHA1 hash of the word ```potatoes```.

When we now enter ```potatoes``` as password, we are disappointed, because it still is not the correct input according to the program. Also, the output string is something totally different than the stored hash and - as already mentionend - might not even be a real hash.

![Imgur](https://i.imgur.com/YAjcy4B.png)

However, when we submit ```potatoes``` as flag, it is accepted and we do not need to worry anymore. Neat.

## 	potatoes
