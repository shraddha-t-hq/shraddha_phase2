
# 1. I like Logic

> I like logic and I like files, apparently, they have something in common, what should my next step be.
<br/> **> This challenge demanded that I decrypt the encoded message in the .sal file using the Salea Logic 2 software.**

## Solution:
Recognising the .sal file, I opened it with Saleae Logic 2 software.Then I chose the appropriate analyzer - `Async Serial`. (After trying out the SPI which yeilded nothing).
This generated a long string of text which I analysed from the terminal to find the flag embedded in it.

<br/>
**Logic workling :**
<img width="1920" height="1128" alt="Screenshot 2025-10-30 195026" src="https://github.com/user-attachments/assets/37658f81-df6f-47d0-bc37-c81ba844fbfc" />

## Flag:
```
FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}
```

## Concepts learnt
-Through this challenge, I learnt how one of the ways of encrypting messages is in the form of wave signals. These signals can further be decrypted by using a software like the Logic 2
## Notes
-For UART protocol --> use Async Serial analyzer.
## Resources
-[https://www.youtube.com/watch?v=Ak9R4yxQPhs]

# 2. IQ Test

## Solution:
![WhatsApp Image 2025-10-31 at 19 39 16_f970da25](https://github.com/user-attachments/assets/95ca469f-dfd3-4aff-b809-0be5eead2086)


## Flag:
```
nite{10001001100}
```

## Concepts learnt
- Logic gate analysis

# 3. Bare Metal Alchemist

> my friend recommended me this anime but i think i've heard a wrong name.
<br/> **> This challenge demanded that I analyze the decompiled data of the .elf file .**

## Solution:
When opened in Ghidra and decompiled, the main method was visible, which gave me the address of the encrypted data(0x68), and the key(0xA5).
Then I used object dump in order to extract all the hex data which fell under the .text section(of which, the encrypted data was a part).
After this I identified the data in 0x68 to be the encoded hex data. This data along with the key was uploaded in an XOR decrypter to generate the flag.

<br/>
**Object dump :**
<img width="1920" height="1128" alt="image" src="https://github.com/user-attachments/assets/e8a667f6-a00e-41d5-820e-1ae0a09d0b1a" />

**Encrypted text :**
> f1e3e6e6f1e3def1cd94d6fa94d6fad6cac896fad694c8d5c996fa91d7c1d094cbcafac394d7c8d291d7c0d8
<br/>
**Decrypter used to get final flag :**
<img width="1920" height="831" alt="Screenshot 2025-11-01 005519" src="https://github.com/user-attachments/assets/425e8668-f362-4e8e-aee6-11b574044aa8" />

## Flag:
```
TFCCTF{Th1s_1s_som3_s1mpl3_4rdu1no_f1rmw4re}
```

## Concepts learnt
- **Ghidra** is a powerful tool that can be used to decompile files in order to understand what it is doing.
- I learned that instead of treating the file as a mysterious block of bytes, you can disassemble and dump the file to reveal both the machine instructions and embedded data. This is done with tools like `avr-objdump`.
- `avr-objdump -s -j .text <file>` prints the raw bytes in the `.text` section, which comes to be useful when encryoted data  is stored inside the code section.

## Resources 
To decrypt the final string with the key :
- [https://www.dcode.fr/xor-cipher]
