[home](https://adminadminctf.github.io/ctf/)

# Number Guess
For this challenge we get a binary (guessPublic64) and the source it was compiled from (guessPublic.c). 


## Outline
The program basically does the following:

* Ask the user to enter the sum (guess) of two secret random numbers in the range from 0 to 1000000
* Calculate the random numbers, using ```c.time()``` as seed
* If the guess equals the sum of the two random numbers, the flag gets printed

Here's the source code:
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>


char *flag = "REDACTED";
char buf[50];

int main(int argc, char **argv) {

	
	puts("Welcome to the number guessing game!");
	puts("Before we begin, please enter your name (40 chars max): ");
	fflush(stdout);
	fgets(buf, 40, stdin);
	buf[strlen(buf)-1] = '\0';
		
	strcat(buf, "'s guess: ");	
	puts("I'm thinking of two random numbers (0 to 1000000), can you tell me their sum?");
	
	srand(time(NULL));
	int rand1 = rand() % 1000000;
	int rand2 = rand() % 1000000;

	printf(buf);
	fflush(stdout);
	int guess;
	char num[8];
	fgets(num,8,stdin);
	sscanf(num,"%d",&guess);

	if (guess == rand1+rand2){
		printf("Congrats, here's a flag: %s\n", flag);
	} else {
		printf("Sorry, the answer was %d. Try again :(\n", rand1+rand2); 
	}
	fflush(stdout);
	return 0;
}
```

From a quick look at the source code we learn that there is no buffer overflow vulnerability due to the use of '''fgets()''' which restricts the length of the input to be read. However, when we enter e.g. ```%d``` as name, our name suddenly becomes something like ```1339265028```. Obviously we're dealing with a format string vulnerability here. This allows us to read the stack and subsequently find out the secret random numbers!

We can outline the approximate steps from here to the flag:
* Using GDB, find out, where on the stack the random numbers are stored (this always the same)
* Craft a format string payload suited to the scenario (and buffer)
* Use this payload on the shell server


## Debugging
We load the binary in GDB and via '''disas main''' obtain the assembly instructions of the main function. For us the two parts where the random numbers are pushed on the stack are of most interest. For this, we just need to find the function calls to the rand() function:

Call for first number: ``` 0x0000000000400930 <+202>:	callq  0x400750 <rand@plt>```
Call for second numb:  ```0x000000000040095f <+249>:	callq  0x400750 <rand@plt>```

From these positions we go down a few steps to find those instructions where the random and modulo operations are finished. In case you are as much of an assembly pro like I am, this might involve a few iterations of trial and error :) . We basically set breakpoints somewhere after the first and second ```rand()``` calls find out the random numbers via ```info reg```.

Below we can see the result of this investigation:
First number moved to %ecx:  ```0x000000000040095a <+244>:	mov    %ecx,%eax```
Second number moved to %ecx: ```0x0000000000400989 <+291>:	mov    %ecx,%eax```

Now we know what our random numbers. Next up is finding out their location on the stack. For this we set another breakpoint after the second breakpoint, prefarrably here: ```0x0000000000400993 <+301>:	mov    $0x0,%eax```
At this breakpoint we display the content of some stackframes via ```x/24d $rsp``` (in this case 24 frames and in decimal representation). Et voilà, there are our two random numbers (see screenshot below).

In order to display both of them, we exactly need to read exactly 9 stack frames, which is achieved with ```%d %d %d %d %d %d %d %d %d``` as name.


Below you can see an exemplary (debug) run of the binary:

![Imgur](https://i.imgur.com/G9RfLsD.png)



And finally, we apply our knowledge on the CTF server. Works like a charm!

![Imgur](https://i.imgur.com/i6wXjxH.png)


## actf{format_stringz_are_pre77y_sc4ry}
