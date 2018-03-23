[home](https://adminadminctf.github.io/ctf/)

# Rop to the Top

We again get an ELF executable and the corresponding source code. ```checksec rop_to_the_top32``` reveals the properties of the binary and its security mechanisms we're facing in this challenge:
*  i386-32-little
*  Partial RELRO
*  No canary found
*  NX enabled
*  No PIE

Upon execution (if we forget to look at the source code^^) we find out that the binary requires a command line argument passed to it. It then states that the input has been copied and exits. If we enter a relatively long string, e.g. 50 characters, we get a segmentation fault which is due to a buffer overflow vulnerability that lets us overwrite the return address of the function. But before exploiting it, we need to find out, what we actually want to achieve.

Via ```objdump -d rop_to_the_top32``` we get an overview of the available functions. We find one interesting function, called "the_top", at ```0x080484db```. As it is never called (verify this in GDB!) we probably need to jump there in order to get to the flag. Our buffer overflow from before can be used for this!

In GDB we determine the size of our buffer by provoking a segmentation fault as before. Then we iteratively reduce the input string length by one until there is no segfault anymore. The unproblematic input size is exactly 44 bytes. To jump to the ```the_top``` function, we need to pass a 44 bytes string and the jump address to the program. 

This may look something like this: 
```./rop_to_the_top32 $(python -c 'print("A"*44+"\xdb\x84\x04\x08")')```

Pew pew pew, the flag!

## actf{strut_your_stuff}
