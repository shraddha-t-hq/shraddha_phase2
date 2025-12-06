# 1. All Signs Allign

**The challenge required to analyze a custom cryptographic scheme.**

## Solution:
The code given encrypted each bit of the flag into a large modulo-p number using quadratic residues and non-residues. 
How the encryption code worked : <br/>
It basically did the following :  <br/>
0 bit --> a·x(mod p) where x is a quadratic residue(QR) <br/>
1 bit --> a·y(mod p) where y = –x mod p is a quadratic non-residue(QNR) <br/>
get_x() picked a quadratic residue(using Euler’s criterion). get_y() returned a quadratic non-residue <br/>
Then each bit of the flag was converted to binary representation and encoded using the functions get_x() and get_y. <br/>

Logic to reverse(decrypt) it : <br/>
Each encrypted value is read from the out.txt. <br/>
Euler’s Criterion is applied to determine if it is a QR or QNR. (using `leg = pow(c, (p-1)//2, p)`)  <br/>
QR AND QNR are mapped back to its origin bits(0/1)  <br/>
All recovered bits are concatenated. Finally bitstring to is converted to an integer then to bytes to recover the flag.  <br/>

I used GPT to write assemble this logic and write the code in python.  <br/>
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

# 2. Residue Refinery

**The challenge required to recover the secret flag from a custom encryption scheme.**

## Solution:
How the encryption code worked (what happened in the code): <br/>
It basically did the following :  <br/>
It Split the flag at nite{...} we had to recover the inside part. <br/>
Then it processed the flag in 2-byte blocks. <br/>
It Used a secret 2-byte key ks=os.urandom(2).
 <br/>
<br/>


**Python decryption code**
```
#!/usr/bin/env python3
# solve.py
# Solver for the "Num" field-multiplication challenge described above.

p = 257

class Num:
    def __init__(self, n):
        # n must be a length-2 iterable of ints (0..256)
        self.n = [int(n[0]) % p, int(n[1]) % p]

    def __mul__(self, other):
        a0, a1 = self.n
        b0, b1 = other.n
        # product polynomial then reduce x^2 -> 3
        prod0 = (a0 * b0 + 3 * (a1 * b1)) % p
        prod1 = (a0 * b1 + a1 * b0) % p
        return Num([prod0, prod1])

    def as_tuple(self):
        return (self.n[0], self.n[1])

    def __repr__(self):
        return f"Num({self.n})"

# multiplicative inverse of element (a,b) in this quadratic extension
def inverse_num(elem: Num) -> Num:
    a, b = elem.as_tuple()
    # matrix M = [[a, 3b],[b,a]]; det = a*a - 3*b*b (mod p)
    det = (a * a - 3 * b * b) % p
    if det % p == 0:
        raise ValueError("Element not invertible (det=0 mod p)")
    inv_det = pow(det, p-2, p)
    A = (a * inv_det) % p
    B = ((-b) * inv_det) % p
    # This corresponds to inverse element = (A, B)
    return Num([A, B])

# Solve key from first known plaintext-ciphertext block
def recover_key_from_block(pt: Num, ct: Num) -> Num:
    # System: [ [pt0, 3*pt1], [pt1, pt0] ] * [k0, k1] = [c0, c1]
    a = pt.n[0]
    b = pt.n[1]
    c0 = ct.n[0]
    c1 = ct.n[1]
    det = (a * a - 3 * b * b) % p
    if det % p == 0:
        raise ValueError("Degenerate system (det=0 mod p) - cannot recover key")
    inv_det = pow(det, p-2, p)
    # inverse matrix * rhs:
    k0 = ( (a * c0 - 3 * b * c1) * inv_det ) % p
    k1 = ( (-b * c0 + a * c1) * inv_det ) % p
    return Num([k0, k1])

def hex_to_bytes(h: str) -> bytes:
    h = h.strip().replace(" ", "").replace("\n","")
    return bytes.fromhex(h)

if __name__ == "__main__":
    # === Edit these if you want to try other ciphertexts/first-plaintext ===
    # Known first plaintext block (as hex): from challenge printed `flag[:2].hex() = '316d'`
    first_plain_hex = "316d"
    # Ciphertext hex from the challenge output (example provided in prompt)
    ct_hex = "9813d3838178abd17836f3e2e752a99d5cd3fba291205f90c1d0a78b6eca"
    # --- end configurable section ---

    ct_bytes = hex_to_bytes(ct_hex)
    if len(ct_bytes) % 2 != 0:
        raise SystemExit("Ciphertext length must be even (2-byte blocks).")

    # prepare first blocks as Num objects (remember: encryption used to_bytes() which reverses)
    # encryption: ct_block = (ks * pt_block).to_bytes() -> stored bytes order is reversed
    # so to recover inner vector from ct bytes, reverse the two bytes
    first_ct_block = ct_bytes[0:2]
    # ct Num internal representation is [ct_byte1, ct_byte0]
    ct0 = Num([first_ct_block[1], first_ct_block[0]])

    pt_bytes = hex_to_bytes(first_plain_hex)
    if len(pt_bytes) != 2:
        raise SystemExit("first_plain_hex must be exactly 2 bytes (4 hex chars).")
    pt0 = Num([pt_bytes[0], pt_bytes[1]])

    # recover key ks such that ks * pt0 = ct0
    ks = recover_key_from_block(pt0, ct0)
    print("Recovered key (ks) =", ks)

    # compute inverse key
    ks_inv = inverse_num(ks)
    print("Inverse key (ks_inv) =", ks_inv)

    # now decrypt all blocks
    recovered = bytearray()
    for i in range(0, len(ct_bytes), 2):
        cblock = ct_bytes[i:i+2]
        cnum = Num([cblock[1], cblock[0]])    # reverse to get internal representation
        pnum = (ks_inv * cnum)                 # ks_inv * ct = plaintext element
        # plaintext internal order is [p0, p1]; append as bytes in that order
        a,b = pnum.as_tuple()
        if a >= 256 or b >= 256:
            raise ValueError("Decrypted element produced value >=256 — invalid for bytes.")
        recovered.extend(bytes([a, b]))

    try:
        rec_text = recovered.decode()
    except:
        rec_text = recovered.hex()
    print()
    print("Recovered inner flag bytes (raw):", recovered)
    print("Recovered inner flag (as text / hex):", rec_text)
    print("Full flag should be: nite{" + rec_text + "}")
```

**Terminal working:** 
```
shraddhatiwari@LAPTOP-F2C51A3F:~$ nano residue.py
shraddhatiwari@LAPTOP-F2C51A3F:~$ python3 residue.py
Recovered key (ks) = Num([60, 6])
Inverse key (ks_inv) = Num([174, 34])

Recovered inner flag bytes (raw): bytearray(b'1mp0r7_m0dul3?_1_4M_7h3_m0dul3')
Recovered inner flag (as text/hex): 1mp0r7_m0dul3?_1_4M_7h3_m0dul3
Full flag should be: nite{1mp0r7_m0dul3?_1_4M_7h3_m0dul3}
```

## Flag:
```
 nite{1mp0r7_m0dul3?_1_4M_7h3_m0dul3}
```

## Concepts learnt:
- 
  ***
# 3. Quixorte

**The challenge required to recover the secret flag from a custom encryption scheme.**

## Solution:
How the encryption code worked (what happened in the code): <br/>
It basically did the following :  <br/>
It Split the flag at nite{...} we had to recover the inside part. <br/>
Then it processed the flag in 2-byte blocks. <br/>
It Used a secret 2-byte key ks=os.urandom(2).
 <br/>
<br/>


**Python decryption code**
```
#!/usr/bin/env python3
# decrypt_with_recovered_key.py
# Place this in the same folder as quote.png.enc and run:
# python3 decrypt_with_recovered_key.py

import sys, zlib
from pathlib import Path

INPUT = "quote.png.enc"
OUT_RAW = "quote_decrypted_raw_unrotated.png"
OUT_FIXED = "quote_decrypted_fixed.png"

if not Path(INPUT).exists():
    print("File not found:", INPUT)
    sys.exit(1)

enc = open(INPUT, "rb").read()
n = len(enc)
print(f"Loaded {INPUT} ({n} bytes)")

# recovered key (hex): ec95e0220a3d5ab7
key = bytes.fromhex("ec95e0220a3d5ab7")
k = len(key)

def rrot(b, i):
    i = i % 8
    return ((b >> i) | ((b << (8 - i)) & 0xFF)) & 0xFF

def lrot(b, i):
    i = i % 8
    return (((b << i) & 0xFF) | (b >> (8 - i))) & 0xFF

# Undo sliding XOR: run the same algorithm used in previous messages
out = bytearray(enc)
for start in range(n - k, -1, -1):
    for j in range(k):
        out[start + j] ^= key[j]

# Undo rotations (encrypt used right-rotate by i; invert by left-rotate by i)
final = bytearray(n)
for i, b in enumerate(out):
    final[i] = lrot(b, i)

open(OUT_RAW, "wb").write(final)
print("Wrote:", OUT_RAW)

# Fix PNG chunk CRCs
PNG_HEADER = bytes([0x89,0x50,0x4E,0x47,0x0D,0x0A,0x1A,0x0A])

def fix_png_crc_bytes(data: bytes) -> bytes:
    if len(data) < 8:
        return data
    ptr = 8
    parts = [data[:8]]
    while ptr + 8 <= len(data):
        length = int.from_bytes(data[ptr:ptr+4], "big")
        ctype = data[ptr+4:ptr+8]
        if ptr + 8 + length + 4 > len(data):
            # truncated chunk — append rest and stop
            parts.append(data[ptr:])
            break
        cdata = data[ptr+8:ptr+8+length]
        crc = zlib.crc32(ctype + cdata) & 0xffffffff
        parts.append(data[ptr:ptr+8+length])  # length+type+data
        parts.append(crc.to_bytes(4, "big"))
        ptr = ptr + 8 + length + 4
        if ctype == b'IEND':
            break
    return b"".join(parts)

fixed = fix_png_crc_bytes(bytes(final))
open(OUT_FIXED, "wb").write(fixed)
print("Wrote:", OUT_FIXED)

# quick sanity: print first 32 bytes of result
print("First 32 bytes of fixed file:", fixed[:32].hex())
try:
    # try reading IHDR
    i = 8
    while i + 8 <= len(fixed):
        ln = int.from_bytes(fixed[i:i+4],"big")
        tp = fixed[i+4:i+8]
        if tp == b'IHDR':
            w = int.from_bytes(fixed[i+8:i+12],"big")
            h = int.from_bytes(fixed[i+12:i+16],"big")
            print("IHDR width,height:", w, h)
            break
        i += 8 + ln + 4
except Exception as e:
    print("Checking IHDR failed:", e)

print("Done. Try opening", OUT_FIXED)

```

**Terminal working:** 
```

```

## Flag:
```
 nite{to_b3_X0R_n0t_t0_b3333}
```

## Concepts learnt:
- 

# 1. All Signs Allign

**The challenge required to analyze a custom cryptographic scheme.**

## Solution:
The code given encrypted 
How the encryption code worked : <br/>
It basically did the following :  <br/>


Logic to reverse(decrypt) it : <br/>


I used GPT to write assemble this logic and write the code in python.  <br/>
<br/>
**Python decryption code**
```
from Crypto.Util.number import *
from sympy import discrete_log

p = 396430433566694153228963024068183195900644000015629930982017434859080008533624204265038366113052353086248115602503012179807206251960510130759852727353283868788493357310003786807

X = 124499652441066069321544812234595327614165778598236394255418354986873240978090206863399216810942232360879573073405796848165530765886142184827326462551698684564407582751560255175
Y = 208271276785711416565270003674719254652567820785459096303084135643866107254120926647956533028404502637100461134874329585833364948354858925270600245218260166855547105655294503224

#Y- 13*X = 37^(m-1)*(37-13)
num = (Y - 13*X) % p
den = (37 - 13)
target = (num * inverse(den, p)) % p

# solve 37^(m-1) = target mod p
m1=discrete_log(p, target, 37)  # gives m-1
ticket = m1 + 1

print("Recovered ticket =", ticket)
print("Recovered flag =", long_to_bytes(ticket))
```

**Terminal working:** 

```
shraddhatiwari@LAPTOP-F2C51A3F:~$ rm ticket.py && nano ticket.py
shraddhatiwari@LAPTOP-F2C51A3F:~$ python3 ticket.py
Recovered ticket = 762035150520137567051383230813374869523369672000904743873872089989543805
Recovered flag = b'nite{g0ld3n_t1ck3t_t0_gl4sg0w}'
```

## Flag:
```
nite{g0ld3n_t1ck3t_t0_gl4sg0w}
```

## Concepts learnt:
- 
  ***

