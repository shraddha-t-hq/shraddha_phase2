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

```ruby
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
```ruby
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
```ruby
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
  
# 5. Re:Draw
> Her screen went black and a strange command window flickered to life, lines of text flashed across before everything went silent. Moments later, the system crashed. By >sheer luck, we recovered a memory dump. 
>Note: There are three stages to this challenge and you will find three flags.
>What we know: just before the crash, a black command window flickered across the screen, something in its output might still be visible if you dig through memory. She was >drawing when it happened, and remnants of a painting program linger, which could reveal more if inspected in the right way. Finally, a mysterious archive hides deeper in >memory, likely holding the last piece of her work.
>Hint:
>Learn up on volatility 2 and its various plugins and what they are used for.

**The challenge demanded that I load and analyze a Windows memory dump using Volatility 2.**


## Solution:

After extracting the memory dump file from the .7z file and learning a bit about volatility 2 commands I first started by identification of the OS to find the right profile. This was done using the command : `volatility -f MemoryDump_Lab1.raw imageinfo`.

It was found that the profile was - `Win7SP1x64`. this would be useful to run the system targeted volatility commands.

This was followed by the assessment of all the processes that wewre going on before the system closure, using - `volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 pslist`
The `pslist` command lists all the processes.

The processes of mspaint and WinRaR fairly stood out to me.
So I investigated further. First the WinRaR process :
the `cmdline` plugin shows the exact command-line used to start a process and the pid specifies the process. It revealed a path to a .rar file.
To investigate it further, I ran the `consoles` plugin.
In the output was what seemed to be a base64 encoded string, which I decoded and turned out to be the first flag!

But the Important.rar file still drew attention, so the `filescan` plugin was used to extract details about the file, like offset. Once the offset was found, I dumped its contents onto a file on my system and saved it as `Important.rar`. Once I started extracting the .rar file, it showed up with a hit for flag 3. The prompt asked for a password- which was the NTLM hash of the account. I found that using `volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 hashdump`, converted it to uppercase and got a `flag3.png` file. <br/>
<img width="500" height="500" alt="flag3" src="https://github.com/user-attachments/assets/0785c13d-ab5d-4554-a460-3e2e53b2b595" />

Now only the second falg remained hidden, now we explore the mspaint side of the memory dump.
I found out the PID of the mspaint process and dumped its contents in a file on my system by using the commands: `volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 memdump -p 2424 -D .` . Then i explored multiple ways to view this data or the message encrypted (like strings ertc.) and finally stumbled upon GIMP.
Manipulating the file in GIMP gave the final flag : 

**GIMP INTERFACE :**
<img width="1920" height="1128" alt="Screenshot 2025-12-05 155111" src="https://github.com/user-attachments/assets/61adda4f-25a3-4b98-9220-445bcc312df6" />
<br/>

**Terminal working:** 

```ruby
shraddhatiwari@LAPTOP-F2C51A3F:~$ cd ./vol
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ 7z x Re_Draw.7z

7-Zip 23.01 (x64) : Copyright (c) 1999-2023 Igor Pavlov : 2023-06-20
 64-bit locale=C.UTF-8 Threads:18 OPEN_MAX:10240

Scanning the drive for archives:
1 file, 158197742 bytes (151 MiB)

Extracting archive: Re_Draw.7z
--
Path = Re_Draw.7z
Type = 7z
Physical Size = 158197742
Headers Size = 146
Method = LZMA2:24
Solid = -
Blocks = 1
Everything is Ok

Size:       1073676288
Compressed: 158197742

shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ ls
 MemoryDump_Lab1.raw  Re_Draw.7z   volatility
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ volatility -f MemoryDump_Lab1.raw imageinfo
Volatility Foundation Volatility Framework 2.6.1
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win7SP1x64, Win7SP0x64, Win2008R2SP0x64, Win2008R2SP1x64_24000, Win2008R2SP1x64_23418, Win2008R2SP1x64, Win7SP1x64_24000, Win7SP1x64_23418
                     AS Layer1 : WindowsAMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/shraddhatiwari/vol/MemoryDump_Lab1.raw)
                      PAE type : No PAE
                           DTB : 0x187000L
                          KDBG : 0xf800028100a0L
          Number of Processors : 1
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0xfffff80002811d00L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2019-12-11 14:38:00 UTC+0000
     Image local date and time : 2019-12-11 20:08:00 +0530
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 kdbgscan
Volatility Foundation Volatility Framework 2.6.1
**************************************************
Instantiating KDBG using: Kernel AS Win7SP1x64 (6.1.7601 64bit)
Offset (V)                    : 0xf800028100a0
Offset (P)                    : 0x28100a0
KDBG owner tag check          : True
Profile suggestion (KDBGHeader): Win7SP1x64
Version64                     : 0xf80002810068 (Major: 15, Minor: 7601)
Service Pack (CmNtCSDVersion) : 1
Build string (NtBuildLab)     : 7601.17514.amd64fre.win7sp1_rtm.
PsActiveProcessHead           : 0xfffff80002846b90 (48 processes)
PsLoadedModuleList            : 0xfffff80002864e90 (140 modules)
KernelBase                    : 0xfffff8000261f000 (Matches MZ: True)
Major (OptionalHeader)        : 6
Minor (OptionalHeader)        : 1
KPCR                          : 0xfffff80002811d00 (CPU 0)

**************************************************
Instantiating KDBG using: Kernel AS Win7SP1x64 (6.1.7601 64bit)
Offset (V)                    : 0xf800028100a0
Offset (P)                    : 0x28100a0
KDBG owner tag check          : True
Profile suggestion (KDBGHeader): Win2008R2SP0x64
Version64                     : 0xf80002810068 (Major: 15, Minor: 7601)
Service Pack (CmNtCSDVersion) : 1
Build string (NtBuildLab)     : 7601.17514.amd64fre.win7sp1_rtm.
PsActiveProcessHead           : 0xfffff80002846b90 (48 processes)
PsLoadedModuleList            : 0xfffff80002864e90 (140 modules)
KernelBase                    : 0xfffff8000261f000 (Matches MZ: True)
Major (OptionalHeader)        : 6
Minor (OptionalHeader)        : 1
KPCR                          : 0xfffff80002811d00 (CPU 0)

**************************************************
Instantiating KDBG using: Kernel AS Win7SP1x64 (6.1.7601 64bit)
Offset (V)                    : 0xf800028100a0
Offset (P)                    : 0x28100a0
KDBG owner tag check          : True
Profile suggestion (KDBGHeader): Win2008R2SP1x64
Version64                     : 0xf80002810068 (Major: 15, Minor: 7601)
Service Pack (CmNtCSDVersion) : 1
Build string (NtBuildLab)     : 7601.17514.amd64fre.win7sp1_rtm.
PsActiveProcessHead           : 0xfffff80002846b90 (48 processes)
PsLoadedModuleList            : 0xfffff80002864e90 (140 modules)
KernelBase                    : 0xfffff8000261f000 (Matches MZ: True)
Major (OptionalHeader)        : 6
Minor (OptionalHeader)        : 1
KPCR                          : 0xfffff80002811d00 (CPU 0)

**************************************************
Instantiating KDBG using: Kernel AS Win7SP1x64 (6.1.7601 64bit)
Offset (V)                    : 0xf800028100a0
Offset (P)                    : 0x28100a0
KDBG owner tag check          : True
Profile suggestion (KDBGHeader): Win2008R2SP1x64_24000
Version64                     : 0xf80002810068 (Major: 15, Minor: 7601)
Service Pack (CmNtCSDVersion) : 1
Build string (NtBuildLab)     : 7601.17514.amd64fre.win7sp1_rtm.
PsActiveProcessHead           : 0xfffff80002846b90 (48 processes)
PsLoadedModuleList            : 0xfffff80002864e90 (140 modules)
KernelBase                    : 0xfffff8000261f000 (Matches MZ: True)
Major (OptionalHeader)        : 6
Minor (OptionalHeader)        : 1
KPCR                          : 0xfffff80002811d00 (CPU 0)

**************************************************
Instantiating KDBG using: Kernel AS Win7SP1x64 (6.1.7601 64bit)
Offset (V)                    : 0xf800028100a0
Offset (P)                    : 0x28100a0
KDBG owner tag check          : True
Profile suggestion (KDBGHeader): Win7SP1x64_23418
Version64                     : 0xf80002810068 (Major: 15, Minor: 7601)
Service Pack (CmNtCSDVersion) : 1
Build string (NtBuildLab)     : 7601.17514.amd64fre.win7sp1_rtm.
PsActiveProcessHead           : 0xfffff80002846b90 (48 processes)
PsLoadedModuleList            : 0xfffff80002864e90 (140 modules)
KernelBase                    : 0xfffff8000261f000 (Matches MZ: True)
Major (OptionalHeader)        : 6
Minor (OptionalHeader)        : 1
KPCR                          : 0xfffff80002811d00 (CPU 0)

**************************************************
Instantiating KDBG using: Kernel AS Win7SP1x64 (6.1.7601 64bit)
Offset (V)                    : 0xf800028100a0
Offset (P)                    : 0x28100a0
KDBG owner tag check          : True
Profile suggestion (KDBGHeader): Win7SP1x64_24000
Version64                     : 0xf80002810068 (Major: 15, Minor: 7601)
Service Pack (CmNtCSDVersion) : 1
Build string (NtBuildLab)     : 7601.17514.amd64fre.win7sp1_rtm.
PsActiveProcessHead           : 0xfffff80002846b90 (48 processes)
PsLoadedModuleList            : 0xfffff80002864e90 (140 modules)
KernelBase                    : 0xfffff8000261f000 (Matches MZ: True)
Major (OptionalHeader)        : 6
Minor (OptionalHeader)        : 1
KPCR                          : 0xfffff80002811d00 (CPU 0)

**************************************************
Instantiating KDBG using: Kernel AS Win7SP1x64 (6.1.7601 64bit)
Offset (V)                    : 0xf800028100a0
Offset (P)                    : 0x28100a0
KDBG owner tag check          : True
Profile suggestion (KDBGHeader): Win7SP0x64
Version64                     : 0xf80002810068 (Major: 15, Minor: 7601)
Service Pack (CmNtCSDVersion) : 1
Build string (NtBuildLab)     : 7601.17514.amd64fre.win7sp1_rtm.
PsActiveProcessHead           : 0xfffff80002846b90 (48 processes)
PsLoadedModuleList            : 0xfffff80002864e90 (140 modules)
KernelBase                    : 0xfffff8000261f000 (Matches MZ: True)
Major (OptionalHeader)        : 6
Minor (OptionalHeader)        : 1
KPCR                          : 0xfffff80002811d00 (CPU 0)

**************************************************
Instantiating KDBG using: Kernel AS Win7SP1x64 (6.1.7601 64bit)
Offset (V)                    : 0xf800028100a0
Offset (P)                    : 0x28100a0
KDBG owner tag check          : True
Profile suggestion (KDBGHeader): Win2008R2SP1x64_23418
Version64                     : 0xf80002810068 (Major: 15, Minor: 7601)
Service Pack (CmNtCSDVersion) : 1
Build string (NtBuildLab)     : 7601.17514.amd64fre.win7sp1_rtm.
PsActiveProcessHead           : 0xfffff80002846b90 (48 processes)
PsLoadedModuleList            : 0xfffff80002864e90 (140 modules)
KernelBase                    : 0xfffff8000261f000 (Matches MZ: True)
Major (OptionalHeader)        : 6
Minor (OptionalHeader)        : 1
KPCR                          : 0xfffff80002811d00 (CPU 0)

shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 pslist
Volatility Foundation Volatility Framework 2.6.1
Offset(V)          Name                    PID   PPID   Thds     Hnds   Sess  Wow64 Start                          Exit 
------------------ -------------------- ------ ------ ------ -------- ------ ------ ------------------------------ ------------------------------
0xfffffa8000ca0040 System                    4      0     80      570 ------      0 2019-12-11 13:41:25 UTC+0000        
0xfffffa800148f040 smss.exe                248      4      3       37 ------      0 2019-12-11 13:41:25 UTC+0000        
0xfffffa800154f740 csrss.exe               320    312      9      457      0      0 2019-12-11 13:41:32 UTC+0000        
0xfffffa8000ca81e0 csrss.exe               368    360      7      199      1      0 2019-12-11 13:41:33 UTC+0000        
0xfffffa8001c45060 psxss.exe               376    248     18      786      0      0 2019-12-11 13:41:33 UTC+0000        
0xfffffa8001c5f060 winlogon.exe            416    360      4      118      1      0 2019-12-11 13:41:34 UTC+0000        
0xfffffa8001c5f630 wininit.exe             424    312      3       75      0      0 2019-12-11 13:41:34 UTC+0000        
0xfffffa8001c98530 services.exe            484    424     13      219      0      0 2019-12-11 13:41:35 UTC+0000        
0xfffffa8001ca0580 lsass.exe               492    424      9      764      0      0 2019-12-11 13:41:35 UTC+0000        
0xfffffa8001ca4b30 lsm.exe                 500    424     11      185      0      0 2019-12-11 13:41:35 UTC+0000        
0xfffffa8001cf4b30 svchost.exe             588    484     11      358      0      0 2019-12-11 13:41:39 UTC+0000        
0xfffffa8001d327c0 VBoxService.ex          652    484     13      137      0      0 2019-12-11 13:41:40 UTC+0000        
0xfffffa8001d49b30 svchost.exe             720    484      8      279      0      0 2019-12-11 13:41:41 UTC+0000        
0xfffffa8001d8c420 svchost.exe             816    484     23      569      0      0 2019-12-11 13:41:42 UTC+0000        
0xfffffa8001da5b30 svchost.exe             852    484     28      542      0      0 2019-12-11 13:41:43 UTC+0000        
0xfffffa8001da96c0 svchost.exe             876    484     32      941      0      0 2019-12-11 13:41:43 UTC+0000        
0xfffffa8001e1bb30 svchost.exe             472    484     19      476      0      0 2019-12-11 13:41:47 UTC+0000        
0xfffffa8001e50b30 svchost.exe            1044    484     14      366      0      0 2019-12-11 13:41:48 UTC+0000        
0xfffffa8001eba230 spoolsv.exe            1208    484     13      282      0      0 2019-12-11 13:41:51 UTC+0000        
0xfffffa8001eda060 svchost.exe            1248    484     19      313      0      0 2019-12-11 13:41:52 UTC+0000        
0xfffffa8001f58890 svchost.exe            1372    484     22      295      0      0 2019-12-11 13:41:54 UTC+0000        
0xfffffa8001f91b30 TCPSVCS.EXE            1416    484      4       97      0      0 2019-12-11 13:41:55 UTC+0000        
0xfffffa8000d3c400 sppsvc.exe             1508    484      4      141      0      0 2019-12-11 14:16:06 UTC+0000        
0xfffffa8001c38580 svchost.exe             948    484     13      322      0      0 2019-12-11 14:16:07 UTC+0000        
0xfffffa8002170630 wmpnetwk.exe           1856    484     16      451      0      0 2019-12-11 14:16:08 UTC+0000        
0xfffffa8001d376f0 SearchIndexer.          480    484     14      701      0      0 2019-12-11 14:16:09 UTC+0000        
0xfffffa8001eb47f0 taskhost.exe            296    484      8      151      1      0 2019-12-11 14:32:24 UTC+0000        
0xfffffa8001dfa910 dwm.exe                1988    852      5       72      1      0 2019-12-11 14:32:25 UTC+0000        
0xfffffa8002046960 explorer.exe            604   2016     33      927      1      0 2019-12-11 14:32:25 UTC+0000        
0xfffffa80021c75d0 VBoxTray.exe           1844    604     11      140      1      0 2019-12-11 14:32:35 UTC+0000        
0xfffffa80021da060 audiodg.exe            2064    816      6      131      0      0 2019-12-11 14:32:37 UTC+0000        
0xfffffa80022199e0 svchost.exe            2368    484      9      365      0      0 2019-12-11 14:32:51 UTC+0000        
0xfffffa8002222780 cmd.exe                1984    604      1       21      1      0 2019-12-11 14:34:54 UTC+0000        
0xfffffa8002227140 conhost.exe            2692    368      2       50      1      0 2019-12-11 14:34:54 UTC+0000        
0xfffffa80022bab30 mspaint.exe            2424    604      6      128      1      0 2019-12-11 14:35:14 UTC+0000        
0xfffffa8000eac770 svchost.exe            2660    484      6      100      0      0 2019-12-11 14:35:14 UTC+0000        
0xfffffa8001e68060 csrss.exe              2760   2680      7      172      2      0 2019-12-11 14:37:05 UTC+0000        
0xfffffa8000ecbb30 winlogon.exe           2808   2680      4      119      2      0 2019-12-11 14:37:05 UTC+0000        
0xfffffa8000f3aab0 taskhost.exe           2908    484      9      158      2      0 2019-12-11 14:37:13 UTC+0000        
0xfffffa8000f4db30 dwm.exe                3004    852      5       72      2      0 2019-12-11 14:37:14 UTC+0000        
0xfffffa8000f4c670 explorer.exe           2504   3000     34      825      2      0 2019-12-11 14:37:14 UTC+0000        
0xfffffa8000f9a4e0 VBoxTray.exe           2304   2504     14      144      2      0 2019-12-11 14:37:14 UTC+0000        
0xfffffa8000fff630 SearchProtocol         2524    480      7      226      2      0 2019-12-11 14:37:21 UTC+0000        
0xfffffa8000ecea60 SearchFilterHo         1720    480      5       90      0      0 2019-12-11 14:37:21 UTC+0000        
0xfffffa8001010b30 WinRAR.exe             1512   2504      6      207      2      0 2019-12-11 14:37:23 UTC+0000        
0xfffffa8001020b30 SearchProtocol         2868    480      8      279      0      0 2019-12-11 14:37:23 UTC+0000        
0xfffffa8001048060 DumpIt.exe              796    604      2       45      1      1 2019-12-11 14:37:54 UTC+0000        
0xfffffa800104a780 conhost.exe            2260    368      2       50      1      0 2019-12-11 14:37:54 UTC+0000        
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 cmdline -p 1512
Volatility Foundation Volatility Framework 2.6.1
************************************************************************
WinRAR.exe pid:   1512
Command line : "C:\Program Files\WinRAR\WinRAR.exe" "C:\Users\Alissa Simpson\Documents\Important.rar"
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 consoles
Volatility Foundation Volatility Framework 2.6.1
**************************************************
ConsoleProcess: conhost.exe Pid: 2692
Console: 0xff756200 CommandHistorySize: 50
HistoryBufferCount: 1 HistoryBufferMax: 4
OriginalTitle: %SystemRoot%\system32\cmd.exe
Title: C:\Windows\system32\cmd.exe - St4G3$1
AttachedProcess: cmd.exe Pid: 1984 Handle: 0x60
----
CommandHistory: 0x1fe9c0 Application: cmd.exe Flags: Allocated, Reset
CommandCount: 1 LastAdded: 0 LastDisplayed: 0
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
Cmd #0 at 0x1de3c0: St4G3$1
----
Screen 0x1e0f70 X:80 Y:300
Dump:
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\SmartNet>St4G3$1
ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=
Press any key to continue . . .
**************************************************
ConsoleProcess: conhost.exe Pid: 2260
Console: 0xff756200 CommandHistorySize: 50
HistoryBufferCount: 1 HistoryBufferMax: 4
OriginalTitle: C:\Users\SmartNet\Downloads\DumpIt\DumpIt.exe
Title: C:\Users\SmartNet\Downloads\DumpIt\DumpIt.exe
AttachedProcess: DumpIt.exe Pid: 796 Handle: 0x60
----
CommandHistory: 0x38ea90 Application: DumpIt.exe Flags: Allocated
CommandCount: 0 LastAdded: -1 LastDisplayed: -1
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
----
Screen 0x371050 X:80 Y:300
Dump:
  DumpIt - v1.3.2.20110401 - One click memory memory dumper
  Copyright (c) 2007 - 2011, Matthieu Suiche <http://www.msuiche.net>
  Copyright (c) 2010 - 2011, MoonSols <http://www.moonsols.com>


    Address space size:        1073676288 bytes (   1023 Mb)
    Free space size:          24185389056 bytes (  23064 Mb)

    * Destination = \??\C:\Users\SmartNet\Downloads\DumpIt\SMARTNET-PC-20191211-
143755.raw

    --> Are you sure you want to continue? [y/n] y
    + Processing...
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ echo "ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=" | base64 -d
flag{th1s_1s_th3_1st_st4g3!!}
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 filescan | grep -i "Important.rar"
Volatility Foundation Volatility Framework 2.6.1
0x000000003fa3ebc0      1      0 R--r-- \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar
0x000000003fac3bc0      1      0 R--r-- \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar
0x000000003fb48bc0      1      0 R--r-- \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar

shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 dumpfiles -Q 0x000000003fa3ebc0 -D . file.data
Volatility Foundation Volatility Framework 2.6.1
DataSectionObject 0x3fa3ebc0   None   \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ ls
Re_Draw.7z       MemoryDump_Lab1.raw  file.None.0xfffffa8001034450.dat   volatility
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ mv file.None.0xfffffa8001034450.dat Important.rar
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ rar x Important.rar

RAR 7.00   Copyright (c) 1993-2024 Alexander Roshal   26 Feb 2024
Trial version             Type 'rar -?' for help

Archive comment:
Password is NTLM hash(in uppercase) of Alissa's account passwd.


Extracting from Important.rar

Enter password (will not be echoed) for flag3.png:
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 hashdump
Volatility Foundation Volatility Framework 2.6.1
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SmartNet:1001:aad3b435b51404eeaad3b435b51404ee:4943abb39473a6f32c11301f4987e7e0:::
HomeGroupUser$:1002:aad3b435b51404eeaad3b435b51404ee:f0fc3d257814e08fea06e63c5762ebd5:::
Alissa Simpson:1003:aad3b435b51404eeaad3b435b51404ee:f4ff64c8baac57d22f22edc681055ba6:::
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ rar x Important.rar

RAR 7.00   Copyright (c) 1993-2024 Alexander Roshal   26 Feb 2024
Trial version             Type 'rar -?' for help

Archive comment:
Password is NTLM hash(in uppercase) of Alissa's account passwd.


Extracting from Important.rar

Enter password (will not be echoed) for flag3.png:

Extracting  flag3.png                                                 OK
All OK
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 pslist
Volatility Foundation Volatility Framework 2.6.1
Offset(V)          Name                    PID   PPID   Thds     Hnds   Sess  Wow64 Start                          Exit 
------------------ -------------------- ------ ------ ------ -------- ------ ------ ------------------------------ ------------------------------
0xfffffa8000ca0040 System                    4      0     80      570 ------      0 2019-12-11 13:41:25 UTC+0000        
0xfffffa800148f040 smss.exe                248      4      3       37 ------      0 2019-12-11 13:41:25 UTC+0000        
0xfffffa800154f740 csrss.exe               320    312      9      457      0      0 2019-12-11 13:41:32 UTC+0000        
0xfffffa8000ca81e0 csrss.exe               368    360      7      199      1      0 2019-12-11 13:41:33 UTC+0000        
0xfffffa8001c45060 psxss.exe               376    248     18      786      0      0 2019-12-11 13:41:33 UTC+0000        
0xfffffa8001c5f060 winlogon.exe            416    360      4      118      1      0 2019-12-11 13:41:34 UTC+0000        
0xfffffa8001c5f630 wininit.exe             424    312      3       75      0      0 2019-12-11 13:41:34 UTC+0000        
0xfffffa8001c98530 services.exe            484    424     13      219      0      0 2019-12-11 13:41:35 UTC+0000        
0xfffffa8001ca0580 lsass.exe               492    424      9      764      0      0 2019-12-11 13:41:35 UTC+0000        
0xfffffa8001ca4b30 lsm.exe                 500    424     11      185      0      0 2019-12-11 13:41:35 UTC+0000        
0xfffffa8001cf4b30 svchost.exe             588    484     11      358      0      0 2019-12-11 13:41:39 UTC+0000        
0xfffffa8001d327c0 VBoxService.ex          652    484     13      137      0      0 2019-12-11 13:41:40 UTC+0000        
0xfffffa8001d49b30 svchost.exe             720    484      8      279      0      0 2019-12-11 13:41:41 UTC+0000        
0xfffffa8001d8c420 svchost.exe             816    484     23      569      0      0 2019-12-11 13:41:42 UTC+0000        
0xfffffa8001da5b30 svchost.exe             852    484     28      542      0      0 2019-12-11 13:41:43 UTC+0000        
0xfffffa8001da96c0 svchost.exe             876    484     32      941      0      0 2019-12-11 13:41:43 UTC+0000        
0xfffffa8001e1bb30 svchost.exe             472    484     19      476      0      0 2019-12-11 13:41:47 UTC+0000        
0xfffffa8001e50b30 svchost.exe            1044    484     14      366      0      0 2019-12-11 13:41:48 UTC+0000        
0xfffffa8001eba230 spoolsv.exe            1208    484     13      282      0      0 2019-12-11 13:41:51 UTC+0000        
0xfffffa8001eda060 svchost.exe            1248    484     19      313      0      0 2019-12-11 13:41:52 UTC+0000        
0xfffffa8001f58890 svchost.exe            1372    484     22      295      0      0 2019-12-11 13:41:54 UTC+0000        
0xfffffa8001f91b30 TCPSVCS.EXE            1416    484      4       97      0      0 2019-12-11 13:41:55 UTC+0000        
0xfffffa8000d3c400 sppsvc.exe             1508    484      4      141      0      0 2019-12-11 14:16:06 UTC+0000        
0xfffffa8001c38580 svchost.exe             948    484     13      322      0      0 2019-12-11 14:16:07 UTC+0000        
0xfffffa8002170630 wmpnetwk.exe           1856    484     16      451      0      0 2019-12-11 14:16:08 UTC+0000        
0xfffffa8001d376f0 SearchIndexer.          480    484     14      701      0      0 2019-12-11 14:16:09 UTC+0000        
0xfffffa8001eb47f0 taskhost.exe            296    484      8      151      1      0 2019-12-11 14:32:24 UTC+0000        
0xfffffa8001dfa910 dwm.exe                1988    852      5       72      1      0 2019-12-11 14:32:25 UTC+0000        
0xfffffa8002046960 explorer.exe            604   2016     33      927      1      0 2019-12-11 14:32:25 UTC+0000        
0xfffffa80021c75d0 VBoxTray.exe           1844    604     11      140      1      0 2019-12-11 14:32:35 UTC+0000        
0xfffffa80021da060 audiodg.exe            2064    816      6      131      0      0 2019-12-11 14:32:37 UTC+0000        
0xfffffa80022199e0 svchost.exe            2368    484      9      365      0      0 2019-12-11 14:32:51 UTC+0000        
0xfffffa8002222780 cmd.exe                1984    604      1       21      1      0 2019-12-11 14:34:54 UTC+0000        
0xfffffa8002227140 conhost.exe            2692    368      2       50      1      0 2019-12-11 14:34:54 UTC+0000        
0xfffffa80022bab30 mspaint.exe            2424    604      6      128      1      0 2019-12-11 14:35:14 UTC+0000        
0xfffffa8000eac770 svchost.exe            2660    484      6      100      0      0 2019-12-11 14:35:14 UTC+0000        
0xfffffa8001e68060 csrss.exe              2760   2680      7      172      2      0 2019-12-11 14:37:05 UTC+0000        
0xfffffa8000ecbb30 winlogon.exe           2808   2680      4      119      2      0 2019-12-11 14:37:05 UTC+0000        
0xfffffa8000f3aab0 taskhost.exe           2908    484      9      158      2      0 2019-12-11 14:37:13 UTC+0000        
0xfffffa8000f4db30 dwm.exe                3004    852      5       72      2      0 2019-12-11 14:37:14 UTC+0000        
0xfffffa8000f4c670 explorer.exe           2504   3000     34      825      2      0 2019-12-11 14:37:14 UTC+0000        
0xfffffa8000f9a4e0 VBoxTray.exe           2304   2504     14      144      2      0 2019-12-11 14:37:14 UTC+0000        
0xfffffa8000fff630 SearchProtocol         2524    480      7      226      2      0 2019-12-11 14:37:21 UTC+0000        
0xfffffa8000ecea60 SearchFilterHo         1720    480      5       90      0      0 2019-12-11 14:37:21 UTC+0000        
0xfffffa8001010b30 WinRAR.exe             1512   2504      6      207      2      0 2019-12-11 14:37:23 UTC+0000        
0xfffffa8001020b30 SearchProtocol         2868    480      8      279      0      0 2019-12-11 14:37:23 UTC+0000        
0xfffffa8001048060 DumpIt.exe              796    604      2       45      1      1 2019-12-11 14:37:54 UTC+0000        
0xfffffa800104a780 conhost.exe            2260    368      2       50      1      0 2019-12-11 14:37:54 UTC+0000        
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 memdump -p 2424 -D .
Volatility Foundation Volatility Framework 2.6.1
************************************************************************
Writing mspaint.exe [  2424] to 2424.dmp
shraddhatiwari@LAPTOP-F2C51A3F:~/vol$ mv 2424.dmp 2424.dat
```

## Flags:
```
flag{th1s_1s_th3_1st_st4g3!!}
flag{G00d_Boy_good_girl}
flag{w3lL_3rd_stage_was_easy}
```

## Concepts learnt:
- Through this challenge, I learned the basic workflow of digital memory forensics when it comes to interpretation using Volatility 
- I learnt that Volatility is plugin-based memory dump analyser. Various plugind like `pslist`,`cmdline`,`hashdump`,`memdump`,`filescan`, etc, some of them requiring additional arguments and specifications
- I figured out how to sequentially explore different parts of memory and carry out Forensic analysis on them. 
 
  ***
