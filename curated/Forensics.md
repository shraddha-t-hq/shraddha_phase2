# 1. Hide and Seek
> Sakamoto’s at it again with a game of hide and seek, but this time, it’s not with Shin or his daughter. An old friend hid some secret data in this image.
> Can you find it before the others do?
>Hint:
>Even in retirement, Sakamoto never loses at hide and seek. Maybe stegseek can help you keep up.

**The challenge demanded that I use stegseek to get the flag.**


## Solution:
Firstly, I used stegseek to find the passphrase for the steghide manipulation. 
Then I used the extracted passphrase in steghide to extract the flag and write the flag into `flag.txt`.
<br/>

**Terminal working:** 

```
shraddhatiwari@LAPTOP-F2C51A3F:~$ stegseek sakamoto.jpg
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "iloveyou1"
[i] Original filename: "flag.txt".
[i] Extracting to "sakamoto.jpg.out".

shraddhatiwari@LAPTOP-F2C51A3F:~$ steghide extract -sf sakamoto.jpg -p iloveyou1
the file "flag.txt" does already exist. overwrite ? (y/n) y
wrote extracted data to "flag.txt".
shraddhatiwari@LAPTOP-F2C51A3F:~$ cat flag.txt
nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}
```

## Flag:
```
nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}
```

## Concepts learnt:
- Through this challenge, I learned how to make effective use of `stegseek`- a software used to extract passphrases using brute-force.
- I learnt how to apply the extracted passphrase to extract data hidden in images using `steghide`.
 
  ***
