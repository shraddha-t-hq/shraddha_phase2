# 1. tunn3l v1s10n
> We found this file. Recover the flag.
<br/>> **The challenge demanded that I interpret the binary of a corrupted file and fix it to accesss the image.**

## Solution:
Initially I opened the file in a HexEditor and interpreting its type. It was a BM(Bitmap) file. Then I saved it as a .bmp file. Upon trying to open the file normally,
there was a format error hinting at corruptions in the binary of the file. I again opened it in HexEditor and scanned for any abnormalities. 

I found that in the first row, the corruption `BA D` was made in the column 0A flowing over to 0B and another such corruption in the column 0E flowing over to 0F.
I checked out the documentation of BM file formats and put in the necessary values.
After doing this, the following picture came up :
<img width="1422" height="439" alt="Screenshot 2025-10-26 151902" src="https://github.com/user-attachments/assets/e07b0015-44bd-4958-8db7-0e4ca28bce9d" />

Now I looked more carefully into the values and tried matching them with the values in the standard documentation. 
By doing this, I found out that `bit map height in pixels` was very low,(present in offset 16) then I played around with the values till I could see the flag in to picture!

**The following HxD snapshot contains all final changes made to the file :**
<img width="1920" height="1128" alt="Screenshot 2025-10-26 152926" src="https://github.com/user-attachments/assets/4361767a-8841-4306-b278-0ae9961e2659" />

**The final picture:**
<img width="895" height="655" alt="Screenshot 2025-10-26 152911" src="https://github.com/user-attachments/assets/8081221a-bde7-45ed-9fb5-67435c3fab70" />
<br/>

## Flag:

```
picoCTF{qu1t3_a_v13w_2020}
```

## Concepts learnt:
- Through this challenge, I learned how to read and adapt data from the documentation of a file type.
- I learnt how to identify and remove corruptions and manipulate physical parameters of the file.

  ## Notes
  - Accidentally deleting bits can cause all bits to shift to the deleted bit's place which completely changes the file. This cannot be undone.
  - Note to self - do not select and backspace when changing values, always select and type.
 
  ***

  # 2. m00nwalk
> Decode this message from the moon.
<br/>> **The challenge demanded that I uncover the message hidden in the sound file.**
## Solution:
To solve this challenge by using an online SSTV signal decoder. 
Upon putting the .wav file into the decoder, it generated a picture with the flag in it.

**The following is the decoder I used:**
<img width="1920" height="1128" alt="Screenshot 2025-10-26 154044" src="https://github.com/user-attachments/assets/cdd60b59-1d61-470e-84d6-96b51ff4543b" />


**The final picture:**
<img width="905" height="723" alt="Screenshot 2025-10-26 154052" src="https://github.com/user-attachments/assets/da883ee6-7834-4edd-815c-c5502694ca9f" />

<br/>

## Flag:

```
picoCTF{beep_boop_im_in_space}
```

## Concepts learnt:
- **S**low **S**can **T**elevision is a method of transmitting images using audio or radio signals. 
- It is a transmission mechanism used to obtain data from space. 

## References 
-[https://www.youtube.com/watch?v=BlJkiMc94CI]

# 3. Trivial Flag Transfer Protocol

> Figure out how they moved the flag.
<br/>> **The challenge demanded that I .**

## Solution:
I opened the given file using Wireshark and then extracted all the TFTP files. It contained `plan` and `instructions` files with encrypted sentences.
I used an encryption identifier to identify as a ROT13 encryption. I then decrypted it using the same software. 
The following were the messages obtained :   
**plan :**
```
IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS
```
**instructions :**
```
TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN
```
<br/>
The files had 3 images, for which I used steghide to decrypt data hidden in the image. Analysing the messages, the key for the decryption was `DUEDILIGENCE`.

**Terminal Working:**
shraddhatiwari@LAPTOP-F2C51A3F:~$ cd ./flag
shraddhatiwari@LAPTOP-F2C51A3F:~/flag$ ls
instructions.txt  picture1.bmp  picture2.bmp  picture3.bmp  plan  program.deb
shraddhatiwari@LAPTOP-F2C51A3F:~/flag$  steghide extract -sf picture1.bmp
Enter passphrase:
steghide: could not extract any data with that passphrase!
shraddhatiwari@LAPTOP-F2C51A3F:~/flag$  steghide extract -sf picture2.bmp
Enter passphrase:
steghide: could not extract any data with that passphrase!
shraddhatiwari@LAPTOP-F2C51A3F:~/flag$  steghide extract -sf picture3.bmp
Enter passphrase:
wrote extracted data to "flag.txt".
shraddhatiwari@LAPTOP-F2C51A3F:~/flag$ ls
flag.txt  instructions.txt  picture1.bmp  picture2.bmp  picture3.bmp  plan  program.deb
shraddhatiwari@LAPTOP-F2C51A3F:~/flag$ cat flag.txt
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}

## Flag:

```
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```

## Concepts learnt:
- I learnt about Trivial File Transfer Protocols.
- I learnt the functioning and use of steghide to extract information from image files.

  ## References
  The following was the site I used for decryption :
  - [https://www.dcode.fr/rot-13-cipher]
