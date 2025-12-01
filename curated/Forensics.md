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


# 2. Nutrela Chunks
>One of my favorite foods is soya chunks. But as I was enjoying some Nutrela today, I noticed a few chunks weren’t quite right. Seems like something’s off with their >structure. Could you help me fix these broken chunks so I can enjoy my meal again?

**The challenge demanded that I analyze and fix the hexdata of the corrupted PNG file.**


## Solution:
Upon clicking on the file its showed that the format was not supported, which hinted at the hex data of the file being corrupted. Taking hints from the description it probably meant that particular `chuncks` were manipulated. <br/>
I opened the file in a hex editor to further inspect it. I tallied the header signature with the standard signature and a few bytes were off making the PNG signature of the file in lowercase. after fixing the header, the problem persisted. I check for other keywords and it turned out that all file definition keywords-like `IHDR`, `IDAT` and `IEND`- were too in lowercase in the strings. So I changed them to the desired values and the image containing the flag was recovered.

**Image generated after successful manipulation:**
<br/>
<img width="512" height="512" alt="nutrela" src="https://github.com/user-attachments/assets/1413f12e-506d-4259-803d-92df859cdbf1" />

<br/>

## Flag:
```
nite{nOw_yOu_knOw_abOut_PNG_chunk5}
```

## Concepts learnt:
- Through this challenge, I learned that there are a few file definition keywords - for PNG: IHDR, IDAT, IEND - which, if manipulated, can corrupt the file.
- These keywords are case sensitive.

 ## Additional resources :
 - [https://www.nayuki.io/page/png-file-chunk-inspector]
  ***


  
