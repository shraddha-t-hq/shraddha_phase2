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

  
# 3. RAR of the Abyss
>Two philosophers peer into the networked abyss and swap a secret. Use the secret to decrypt the Abyss’ RAwR and pull your flag from the void.ced a few chunks weren’t quite >right. Seems like something’s off with their >structure. Could you help me fix these broken chunks so I can enjoy my meal again?

**The challenge demanded that I manipulate a .pcap file to extract a password and encoded data. Then decode it to get the flag.**


## Solution:
Firstly, I opened the .pcap file in Wireshark. I filtered out the TCP files and `followed` the TCP channels to find a conversation and eventually the password and encoded data. 

**Password :**
```
Nietzsche: b3y0ndG00dand3vil
```

**Image of the encoded data in a TCP channel:**
<br/>
<img width="1284" height="1129" alt="Screenshot 2025-12-01 125755" src="https://github.com/user-attachments/assets/efa67deb-490b-4861-aa21-504f9e21720e" />

Then I decoded this data by putting it into a .rar file and extracting using the password found.


**Terminal working:**
```
shraddhatiwari@LAPTOP-F2C51A3F:~$ echo "526172211a07010094ceedc121040000010f644537b9278a61e25503b4957506e3674391aa0766e843dca3d75f52dc08f7774f6470a0a32680ccd66b612ae47347d80e85f7a2d1907178728fa65de05fe2ec158a6fb40be196b2fe5b2abbe7f1568c445080a86b1b49eebe54ef777e1886c796fabafd16e51baaa431b1c0ed3a6f3a0700a6c34b4f64ea7103e939aff7a95a9e9b2ea0b87cbc186159a252d0048f5b1da871387f8f930fbde37df8dd1e41a8215c00b045e59f374c21e67c9f1dd02fb983ee101784fadaa92929ed7b6a61c4da9d9ffa7487f7e6853dadd1ebf40b1998b716f0ab41d19d1b6a25000c58c2fc96f9b0c4cdcfa7ee79b2ef926b051dff7132a4c4a3f84b75dbd9760b67fba3cb5c797bd7bdf1d7de52a94a7f" | xxd -r -p > rawr.rar
shraddhatiwari@LAPTOP-F2C51A3F:~$ ls | grep rawr.rar
rawr.rar
shraddhatiwari@LAPTOP-F2C51A3F:~$ rar x rawr.rar -pb3y0ndG00dand
3vil

RAR 7.00   Copyright (c) 1993-2024 Alexander Roshal   26 Feb 2024
Trial version             Type 'rar -?' for help


Extracting from rawr.rar


Would you like to replace the existing file flag.txt
    42 bytes, modified on 2025-11-30 22:47
with a new one
    47 bytes, modified on 2025-10-22 13:53

[Y]es, [N]o, [A]ll, n[E]ver, [R]ename, [Q]uit y

Extracting  flag.txt                                                  OK
All OK
shraddhatiwari@LAPTOP-F2C51A3F:~$ cat flag.txt
nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}
```
<br/>

## Flag:
```
nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}
```

## Concepts learnt:
- Through this challenge, I refined my network forensics skills and the concept of Transport Control Protocol.
- I also learnt how to handle and extract data from encoded .rar files

  ***

# 4. Hide and Seek
> Looks like I got a little too clever and hid the flag as a password in Firefox, tucked away like one of NineTails’ many tails.
> Recover the "logins" and the "key4" and let it guide you to the flag.
> Hint:
> I named my Ninetails "j4gjesg4", quite a peculiar name isn't it?

**The challenge demanded that I extract firefox passwords in plaintext.**

## Solution:
Firstly, I used FTK Imager in order to expand the `ninetails.ad1` file(extracted from `ninetails.rar`).
Then I went into the only user folder present there : `GIC2024`.
the following is the path I followed to reach the firefox keys4 and logins file :
GIC2024 --> AppData --> Roaming --> Mozilla --> Firefox --> Profiles --> j4gjesg4.default-release\
<br/>
<img width="1422" height="808" alt="Screenshot 2025-12-02 222435" src="https://github.com/user-attachments/assets/266a0279-cebf-4971-a206-40354b6457a6" />
<br/>
Then I used a firefox decoder from the following github repository : [https://github.com/unode/firefox_decrypt.git]
This was used to decode the passwords from the profile and extract the flag. The following is the terminal working : 
**Terminal working:** 
```
shraddhatiwari@LAPTOP-F2C51A3F:~$ cd ./nt
shraddhatiwari@LAPTOP-F2C51A3F:~/nt$ git clone https://github.com/unode/firefox_decrypt.git
Cloning into 'firefox_decrypt'...
remote: Enumerating objects: 1482, done.
remote: Counting objects: 100% (392/392), done.
remote: Compressing objects: 100% (103/103), done.
remote: Total 1482 (delta 319), reused 329 (delta 278), pack-reused 1090 (from 2)
Receiving objects: 100% (1482/1482), 519.97 KiB | 4.37 MiB/s, done.
Resolving deltas: 100% (916/916), done.
shraddhatiwari@LAPTOP-F2C51A3F:~/nt$ ls
firefox_decrypt  key4.db  logins.json
shraddhatiwari@LAPTOP-F2C51A3F:~/nt$ cd ./firefox_decrypt
shraddhatiwari@LAPTOP-F2C51A3F:~/nt/firefox_decrypt$ ls
CHANGELOG.md     LICENSE    firefox_decrypt.py  tests
CONTRIBUTORS.md  README.md  pyproject.toml
shraddhatiwari@LAPTOP-F2C51A3F:~/nt/firefox_decrypt$ python ./firefox_decrypt.py
Command 'python' not found, did you mean:
  command 'python3' from deb python3
  command 'python' from deb python-is-python3
shraddhatiwari@LAPTOP-F2C51A3F:~/nt/firefox_decrypt$ mv ~/nt/ninetails ~/nt/firefox_decrypt/
shraddhatiwari@LAPTOP-F2C51A3F:~/nt/firefox_decrypt$ python3 fir
efox_decrypt.py ninetails
2025-12-02 16:53:53,269 - WARNING - profile.ini not found in ninetails
2025-12-02 16:53:53,269 - WARNING - Continuing and assuming 'ninetails' is a profile location

Website:   https://www.rehack.xyz
Username: 'warlocksmurf'
Password: 'GCTF{m0zarella'

Website:   https://ctftime.org
Username: 'ilovecheese'
Password: 'CHEEEEEEEEEEEEEEEEEEEEEEEEEESE'

Website:   https://www.reddit.com
Username: 'bluelobster'
Password: '_f1ref0x_'

Website:   https://www.facebook.com
Username: 'flag'
Password: 'SIKE'

Website:   https://warlocksmurf.github.io
Username: 'Man I Love Forensics'
Password: 'p4ssw0rd}'
```

## Flag:
```
GCTF{m0zarella_f1ref0x_p4ssw0rd}
```

## Concepts learnt:
- Through this challenge, I learned about password safety in a browser - especially Firefox.
- I learnt how to extract and analyze a forensic image using FTK Imager, how to navigate Windows directory structures inside an acquired image and gained an understanding of how user data is stored.
- I learnt how Firefox's NSS stores encrypted data using keys(stored in key4.db)
- I learnt how to extract passwords from a Firefox profile through .json and .db files.
  
  ***
  
