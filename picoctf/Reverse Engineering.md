# 1. GDB Baby Step 1

> Can you figure out what is in the eax register at the end of the main function? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.
Disassemble [this].
<br/>> **The challenge demanded that I disassemble a code, and interpret its assembly language code.**

## Solution:
I first made the file executable using `chmod` command. Then I made use of **`gdb`** to disassemble the program. The last occurance of `%eax` was identified and the hex number was noted. The hex number was then converted to decimal and placed in flag format. 

**Terminal working:**
```
shraddhatiwari@LAPTOP-F2C51A3F:~$ chmod a+x d.elf
shraddhatiwari@LAPTOP-F2C51A3F:~$ ls -l
total 136
-rwxr-xr-x 1 shraddhatiwari shraddhatiwari 16472 Oct 26 18:57  d.elf
-rw-r--r-- 1 shraddhatiwari shraddhatiwari    25 Oct 26 19:11 'debugger0_a (1):Zone.Identifier'
-rwxr-xr-x 1 shraddhatiwari shraddhatiwari  3286 Oct 24 09:20  decode_flag.py.txt
-rw-r--r-- 1 shraddhatiwari shraddhatiwari  8724 Oct 26 06:45  firmware.elf
-rw-r--r-- 1 shraddhatiwari shraddhatiwari    25 Oct 26 07:01  firmware.elf:Zone.Identifier
drwxr-xr-x 2 shraddhatiwari shraddhatiwari  4096 Oct  5 11:10  hill
-rw------- 1 shraddhatiwari shraddhatiwari   432 Sep 22 11:03  key
-rw-r--r-- 1 shraddhatiwari shraddhatiwari   112 Sep 22 11:03  key.pub
-rw-r--r-- 1 shraddhatiwari shraddhatiwari  4089 Oct  9 17:42  programpy.py
-rw-r--r-- 1 shraddhatiwari shraddhatiwari  1184 Oct 25 13:34  resp_18.html
-rwxr-xr-x 1 shraddhatiwari shraddhatiwari  6708 Oct 24 15:28  rsa_oracle_exploit.py.txt
drwx------ 3 shraddhatiwari shraddhatiwari  4096 Oct  9 13:57  snap
-rwxr-xr-x 1 shraddhatiwari shraddhatiwari 56560 Oct  6 11:12  tameimpala
-rw-r--r-- 1 shraddhatiwari shraddhatiwari    25 Oct  6 11:25 'tameimpala (1):Zone.Identifier'
-rw-r--r-- 1 shraddhatiwari shraddhatiwari     0 Oct  7 20:46  x.sh
shraddhatiwari@LAPTOP-F2C51A3F:~$ gdb d.elf
GNU gdb (Ubuntu 15.0.50.20240403-0ubuntu1) 15.0.50.20240403-git
Copyright (C) 2024 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from d.elf...
(No debugging symbols found in d.elf)
(gdb) disassemble main
Dump of assembler code for function main:
   0x0000000000001129 <+0>:     endbr64
   0x000000000000112d <+4>:     push   %rbp
   0x000000000000112e <+5>:     mov    %rsp,%rbp
   0x0000000000001131 <+8>:     mov    %edi,-0x4(%rbp)
   0x0000000000001134 <+11>:    mov    %rsi,-0x10(%rbp)
   0x0000000000001138 <+15>:    mov    $0x86342,%eax
   0x000000000000113d <+20>:    pop    %rbp
   0x000000000000113e <+21>:    ret
End of assembler dump.
(gdb) q

```
<br/>

## Flag:

```
picoCTF{549698}
```

## Concepts learnt:
- gdb is a powerful reverse engineering tool. It is a debugger that can be used to closely follow the working of a program and the real time working in assembly level language.
- While doing this challenge and learning about gbd, i also encountered other commands, like `run`, `list`, `n` and how to set up breakpoints and their use.

## Resources:

- [https://www.youtube.com/watch?v=sCtY--xRUyI]
- [https://www.youtube.com/watch?v=Dq8l1_-QgAc]


***
# 2. Vault door 3

> This vault uses for-loops and byte arrays. The source code for this vault is here: VaultDoor3.java.
<br/>> **The challenge demanded that I interpret what the Java code was doing, and reverse engineer it to find the flag.**

## Solution:
Upon the initial assessment of the code, I inferred that there was a function that checked for the correct password by jumbling the user-inputted password and checking for equality with the jumbled correct password(thus not actually revealing the password anywhere in the code).
Now, to solve this problem, I took the snippet of the password checker function in a VS Code .java file and modified it a bit, so that when I entered the jumbled password that the program used for equality check, it would unjumble it and give me the password. Since the manipulation was just replacing indexes, putting the password through the series of manipulation loops would give the original password.

**Java Code**
```
class VaultDoor3 {
    public static void main(String args[]) {
        char[] buffer = new char[32];
        int i ;
        String password = "jU5t_a_sna_3lpm18gb41_u_4_mfr340";
        for (i=0; i<8; i++) {
            buffer[i] = password.charAt(i);
        }
        for (; i<16; i++) {
            buffer[i] = password.charAt(23-i);
        }
        for (; i<32; i+=2) {
            buffer[i] = password.charAt(46-i);
        }
        for (i=31; i>=17; i-=2) {
            buffer[i] = password.charAt(i);
        }
        String s = new String(buffer);
        
        System.out.println(s);
        
    }
}
```
<br/>

## Flag:

```
picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_1fb380}
```

## Concepts learnt:
- Through this challenge, I learnt how from a provided code, I can extract useful code and reverse what the code does by changing up a few things in order to get what is useful.
- I also brushed up my skills on how to trace back steps and do operations in reverse to obtain untampered data. 

# 3. ARMssembly

> For what argument does this program print `win` with variables 83, 0 and 3? File: chall_1.S Flag format: picoCTF{XXXXXXXX} -> (hex, lowercase, no 0x, and 32 bits. ex. 5614267 would be picoCTF{0055aabb})
<br/>> **This challenge demanded me to interpret an assembly code and find out the argument that prints 'You win'**

## Solution:
Firstly, I tried to read the assembly code and understand what was going on. 
I found out that through the `main` function, a user-entered argument was passed into the function `func`. The value returned by `func` was checked for equality with 0, if 0, the win statement would print.

**The following is the working I did on `func` in order to get the correct return value**
```
func:
	sub	sp, sp, #32
	str	w0, [sp, 12]  ; [sp,12]=w0
	mov	w0, 83        ; w0=83
	str	w0, [sp, 16]  ; [sp, 16]=83
	str	wzr, [sp, 20] ; [sp,20]=0
	mov	w0, 3         ; w0=3
	str	w0, [sp, 24]  ;[sp,24]=3
	ldr	w0, [sp, 20]  ; w0=0
	ldr	w1, [sp, 16]  ; w1=83
	lsl	w0, w1, w0    ; w0 = w1<<w0 = 83<<0=83
	str	w0, [sp, 28]  ; [sp,28]=83
	ldr	w1, [sp, 28]  ;w1=83
	ldr	w0, [sp, 24]  ; wo=3
	sdiv	w0, w1, w0 ; w0 = w1 // w0 = 27
	str	w0, [sp, 28] ; [sp,28] = 27
	ldr	w1, [sp, 28] ; w1=27
	ldr	w0, [sp, 12] ; w0=argument passed to the function
	sub	w0, w1, w0 ; w0=w1-w0 = 27-arg
	str	w0, [sp, 28] ; [sp,28]= 27-arg
	ldr	w0, [sp, 28] ; w0= 27-arg
	add	sp, sp, 32 ; 
	ret ; return w0

```
<br/>

## Flag:

```
picoCTF{0000001b}
```

## Concepts learnt:
- To do this challenge, I learnt some important assembly language commands and their working.
- I also learnt how control flows in an assembly code
