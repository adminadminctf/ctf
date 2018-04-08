[home](https://adminadminctf.github.io/ctf/)

# Data Exfil
We're given a ZIP archive which contains various log files and a packet capture. I only used the PCAP for this challenge, so I cannot tell if the log files are of any help. First we try the usual stuff when dealing with a packet capture: Following TCP and HTTP streams, trying to export transmitted objects via File -> Export Objects. However, these steps do not lead to anything useful here.

When we look at the different protocols, we discover, that the host ```192.168.198.128``` uses two different Servers for DNS: ```8.8.8.8``` (Google) and ```54.175.216.124```. While the responses from the first DNS server look legit (what could go wrong with Google?), those of the second server have weird subdomains in the URLs. We filter these responses in Wireshark with ```ip.src == 54.175.216.124``` and order the result ascending based on packet number.

![Imgur](https://i.imgur.com/RfwsOLe.png)

Then we copy two or three packets (e.g. 180, 182 and 184) as printable text and throw the subdomains into a hex decoder. Below you can see the three subdomains:

```
504b030414000000000030be774c973a10791e0000001e0000000a000000
7365637265742e74787473756e7b7730775f495f646e735f7333335f7930
755f316e5f683372337d504b0102140014000000000030be774c973a1079
```

and their decoded pendants which contain the flag:

```
PK........0¾wL.:.y........
...secret.txtsun{w0w_I_dns_s33_y0u_1n_h3r3}PK..........0¾wL.
```


## sun{w0w_I_dns_s33_y0u_1n_h3r3}
