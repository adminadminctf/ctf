[home](https://adminadminctf.github.io/ctf/)

# Bomba
The challenge instructions come with everything we need to successfully decipher the encrypted message:
![Imgur](https://i.imgur.com/6kiP02y.png)

and

```
UCF 07 ZERO 0800 = 50 = MHW FLC = LWB VJT LWD QYB JMS AMU RBO QXY SBZ EYN RLG RNK KVQ YJK EKR GMS CYB MH=
```

The first part is not really of interest. ```50``` indicates the length of the message, ```MHW``` is the initial rotor position, ```FLC``` is the encrypted message key value and the first 3-character block of the last part, ``` LWB ```,  is the Kenngruppen value which determines the rotor-, reflector-, ring- and plugboard-settings for the day. ```LWB``` is a Kenngruppen value of day 7. Now we can just look up day 7 in the table and initialize our Enigma with these settings. 

Note: Don't forget to remove ```LWB``` from the ciphertext in order to decrypt the message correctly.


```
from enigma.machine import EnigmaMachine

machine = EnigmaMachine.from_key_sheet(
    rotors = 'V I IV',
    reflector = 'B',
    ring_settings = [18, 11, 25],
    plugboard_settings = 'TS IK AV QP HW FM DX NG CY UE')

ciphertext = 'VJTLWDQYBJMSAMURBOQXYSBZEYNRLGRNKKVQYJKEKRGMSCYBMH'
    
machine.set_display('MHW')                                # set initial rotor position
msg_key = machine.process_text('FLC')               # decrypt the message key value
machine.set_display(msg_key)
plaintext = machine.process_text(ciphertext)

print(plaintext)
```


Output: DAMNXTHATXBOMBAXKRYPTOLOGICZNAXANDXMARIANXREJEWSKI

## sun{DAMNXTHATXBOMBAXKRYPTOLOGICZNAXANDXMARIANXREJEWSKI}
