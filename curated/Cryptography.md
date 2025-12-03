# 1. All Signs Allign

**The challenge required to analyze a custom cryptographic scheme.**

## Solution:
The code given encrypted each bit of the flag into a large modulo-p number using quadratic residues and non-residues. 
How the encryption code worked : 
It basically did the following : 
0 bit --> a·x(mod p) where x is a quadratic residue(QR)
1 bit --> a·y(mod p) where y = –x mod p is a quadratic non-residue(QNR)
get_x() picked a quadratic residue(using Euler’s criterion). get_y() returned a quadratic non-residue
Then each bit of the flag was converted to binary representation and encoded using the functions get_x() and get_y.

Logic to reverse(decrypt) it :
Each encrypted value is read from the out.txt.
Euler’s Criterion is applied to determine if it is a QR or QNR. (using `leg = pow(c, (p-1)//2, p)`)
QR AND QNR are mapped back to its origin bits(0/1)
All recovered bits are concatenated. Finally bitstring to is converted to an integer then to bytes to recover the flag.

I used GPT to write assemble this logic and write the code in python.
<br/>
**Python decryption code**
```
from Crypto.Util.number import long_to_bytes

p=9129026491768303016811207218323770273047638648509577266210613478726929333106121387323539916009107476349319902011390210650434835260358014251332047605739279

with open("out.txt") as f:
    out=eval(f.read().strip())

bits = ""

for c in out:
    leg = pow(c, (p-1)//2, p)
    
    if leg == 1:
        bits += "1"
    else:
        bits += "0"

flag_int = int(bits, 2)
flag = long_to_bytes(flag_int)

print(flag)
```

**Terminal working:** 

```
shraddhatiwari@LAPTOP-F2C51A3F:~$ cd ./asi
shraddhatiwari@LAPTOP-F2C51A3F:~/asi$ nano ASI.py
shraddhatiwari@LAPTOP-F2C51A3F:~/asi$ python3 ASI.py
b'nite{r3s1du35_f4ll1ng_1nt0_pl4c3}'
```

## Flag:
```
nite{r3s1du35_f4ll1ng_1nt0_pl4c3}
```

## Concepts learnt:
- How quadratic residues work:
 - For a prime modulus 𝑝, Euler’s criterion tells you whether a value is a QR or a QNR.
- How to reconstruct a message bit-by-bit by evaluating each ciphertext value with Euler’s criterion, and extracting a full bitstring, Converting those bits into an integer and then into bytes reconstructed the flag.
  ***

