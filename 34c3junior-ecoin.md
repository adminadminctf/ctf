[home](https://adminadminctf.github.io/ctf/)

# Ecoin - hard
This challenge was quite a ride, but probably also the most fun one of the JuniorCTF. We have never done something similar before, so please excuse if some of our steps appear dilettante.

## Extracting all the interesting stuff
###  Hex editor
When we open the PDF we see nothing suspicious, besides the word *Password* written white on white at the bottom of page 2. But no flag. Would have been too easy! Next, we throw the PDF into a hex editor of our choice. At offset 01434976 the string *hint.pdf* can be seen - a coincidence? At offset 01435056 there is an xref to offset 1287603. So let us look at that: A few bytes down the road at 01288064 we see the string *flag.png*. A coincidence again? Probably not!

### Unzip
We try several steps which do not lead to something useful. So why not unzip? Unzipping the PDF leaves us with 2 new files, *Object 15.tiff*, *Object 54.jpg*. So far, so good. 

### Binwalk
As it seems, we need to dig a little deeper as different unzippers (i.e. on Linux, Windows, Mac) deliver different unzipping results. So we use binwalk which helps us extracting all the other stuff we did not find so far.

```
binwalk -e ecoin_vuln_notes.pdf
```

Binwalk extracts *13A75C.zip*, a *flag.png* and about 40 other files most of which are not of interest.

## Interesting files
Now let us take a look at what we got so far.

### Object 54.jpg
This a very colorful illustration with the statement that "others files are *Pure_Funk*". In fact, *Pure_Funk* is the password for *13A75C.zip*.

![Imgur](https://i.imgur.com/kDkFNgH.jpg)

### 13A75C.zip
When we unzip this using *Pure_Funk* as password we end up with the *hint.pdf*.

### flag.png
This likely contains the flag as the text in the PNG starts with *34C3_*. But we're not quite done yet, as the flag still needs to be extracted from the superfluous characters.

![Imgur](https://i.imgur.com/xHIFVZN.png)


### hint.pdf
Unpacked from *13A75C.zip*, this PDF contains two essential things:
1. An AES Initialization Vector hidden on top of the page: *AES IV: F01D86CDBB7E1CD88815BEB4106A558C* (find via CTRL+A)
2. The actual hint that *AngeWouldLoveIt!*

![flag.png](https://i.imgur.com/jMCVvxP.png)


Now would be a good time to look up the name *Ange* in context of CCC. After some googleing we learn, that this hint refers to Ange Albertini, a very cool guy who you should definitively check out for his awesome and fun talks/projects. Of special interest for this challenge is this [slide set](https://speakerdeck.com/ange/funky-file-formats-31c3) from 31C1 called *Funky File Formats* (note the resemblence to our *Object 54.jpg* :). From the slides we learn how to hide (and also extract) files in and from other files - in this case images - using various encryption ciphers and hand-crafted IVs. Great, that is what our previously found IV is for!


## Extract the hidden raster (AngeCrypt)
Now to the final steps. We somehow need to en- or decrypt the *flag.png* in order to find something that helps obtaining the actual flag. To do this we use the python script below:

```
from Crypto.Cipher import AES

key = 'AngeWouldLoveIt!'  # key found in hint.pdf
iv = '\xF0\x1D\x86\xCD\xBB\x7E\x1C\xD8\x88\x15\xBE\xB4\x10\x6A\x55\x8C' # initialization vector also found in hint.pdf

cipher = AES.new(key, AES.MODE_CBC, iv)

with open('flag.png', "rb") as f:
    flag = f.read()

encrypted = cipher.encrypt(flag)

with open('decrypted.png', "wb") as f:
    f.write(encrypted)
```

 It basically takes *AngeWouldLoveIt!* from *hint.pdf* as key and *F01D86CDBB7E1CD88815BEB4106A558C* also from *hint.pdf* as IV. The cipher used in this challenge is AES in CBC mode, but it also could have been for example DES or 3DES. However, as the *hint.pdf* stated *AES IV* we just trust it and go with that. Also, whether we need to de- or encrypt can be found out just by trial and error. In this case we need to encrypt.
 
Finally, executing this script rewards us with the last missing piece: a raster which can be placed on top of the *flag.png* in order to reveal the flag.

![Imgur](https://i.imgur.com/wSYhVOi.png)


Note that we changed the colour of this raster from white to grey in Gimp in order to better see it here on ctftime. To place the raster on top of *flag.png* just use Gimp or Photoshop and open it as layer in *flag.png* . Voilà, the final result:

![Imgur](https://i.imgur.com/qwoIn4S.png)

## 34C3_F1L3_FORMA7S_AR3_COMMUN17Y_CONNEC7ORS
