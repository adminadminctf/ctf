[home](https://adminadminctf.github.io/ctf/)

# Information society - easy

This challenge was really easy - but only if you were born before 1990 or already knew the band Information Society prior to 34C3. tl,dr: The flag is a ```PNG``` picture file which was encoded into audio modem tones (This noise sounded so familiar!).

After trying 10000 steps which did not lead to anything useful, we duckduckwent to the internet for help. As it turned out, Information Society is a band who did something similar back in 1992. They basically modulated the lyrics of an easter egg track and put the resulting audio file on their *Peace And Love INC.* album.

That's all we need to know! Thankfully we do not need a modem. Instead we just use a software called minimodem by [Kamal Mostafa](http://www.whence.com/minimodem). We feed minimodem our ```information_society_bonustrack.flac```, copy+pasta the minimodem parameters from the [video title](https://www.youtube.com/watch?v=y3ZT-1y4eeY) and redirect the command line output into a file. By the way, ```minimodem --help``` might be useful, if you are interested in what all these parameters mean.

```minimodem --rx -8 300 -M 2225 -S 205 -f information_society_bonustrack.flac > output```

After the program is finished, we check the magic number of ouf file or simply use file command and see, that we got a ```PNG```! Finally, open it up - et voilà, our flag:

![Imgur](https://i.imgur.com/Ot3lGYK.png)

## 34C3_tut_tut_wat!
