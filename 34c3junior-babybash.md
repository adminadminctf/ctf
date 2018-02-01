[home](https://adminadminctf.github.io/ctf/)

# Babybash - easy
In this challenge we are required to execute a simple command in a reduced bash in order to obtain the flag.
Below you can see the output of the `help` command which displays the command we want to execute and also which characters are banned from usage.

The bash can be reached via `nc 35.189.118.225 1337` 

![Imgur](https://i.imgur.com/QmYnl3a.png)

Our first intution was that we need to encode the command in some way or another. We did not think of capital letters, as the other writeup suggests. D'oh!

So we encode our command in octal representation since there is no alphabetical letter needed (in contrast to hex encoding). The encoded command is stored in a variable in the first step and finally executed in a second step.

After executing the octal representation of `/get_flag` we learn that we also need to pass the parameter `gimme_FLAG_please` in order to receive the flag. Below our full command, `/get_flag gimme_FLAG_please`:

```
__=$'\057\147\145\164\137\146\154\141\147\040\147\151\155\155\145\137\106\114\101\107\137\160\154\145\141\163\145'; $__
```

Note that the variable could also be a capital letter. But why take the easy path, if there is a more complicated one? Anyways, here's the result:

## 34C3_LoOks_lik3_y0U_are_nO_b4by_4ft3r_4ll
