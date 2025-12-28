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
```ruby
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

**The challenge required reversing a custom quadratic field multiplication scheme to recover a hidden key and decrypt the flag.**

## Solution:
The challenge implemented a custom encryption scheme using multiplication inside a quadratic extension field modulo p = 257.
Each plaintext block of 2 bytes was treated as an element (a, b) in the field Fₚ[x] / (x² − 3), and encryption simply multiplied this element by a secret key element ks in the same field.
Because multiplication in this field uses the rule x² = 3 (mod p), the ciphertext looked like completely scrambled byte pairs.

How the encryption worked: <br/>
It represented every 2-byte block of the flag as (pt₀, pt₁), interpreted as a field element. <br/>
A fixed 2-byte secret key (k₀, k₁) was chosen. <br/>
Ciphertext was produced by computing ks × pt, using the custom multiplication where
x² → 3, so the product of two elements (a, b) and (c, d) becomes:

first component = a·c + 3·b·d (mod 257) <br/>

second component = a·d + b·c (mod 257) <br/>
Finally, each product (u, v) was written to bytes in reversed order before output.

Logic to reverse (decrypt) it: <br/>
A known-plaintext block (“1m” = 316d in hex) was used to recover the key. <br/>
From the first 2 ciphertext bytes, the solver reversed the byte-order and obtained the field element (c₀, c₁). <br/>
Since encryption was ks × pt = ct, a simple linear system was solved to recover the key (k₀, k₁) using modular arithmetic. <br/>
After recovering the key, its multiplicative inverse in this quadratic field was computed. <br/>
Each ciphertext block (c₀, c₁) was then multiplied by ks⁻¹, restoring the plaintext block. <br/>
All blocks were decoded in this way, giving back the inner flag string. <br/>

I used GPT to assemble the mathematical steps, derive the inverse element, and write the Python code that automated key recovery and decryption. <br/>
 <br/>
<br/>


**Python decryption code**
```ruby
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
```ruby
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
- I learned how encryption using a quadratic extension field works, representing plaintext blocks as 2-byte elements and multiplying them by a secret key inside the field. I understood how modular arithmetic and field-specific multiplication rules can scramble data, and how a known-plaintext attack allows recovery of the key by solving a simple modular linear system. I also learned how to compute the multiplicative inverse of an element in a quadratic field, and how applying it to ciphertext blocks restores the original plaintext, demonstrating the practical application of number theory and field arithmetic in cryptanalysis.
  ***
# 3. Quixorte

**The challenge required reversing a custom byte-rotation and sliding-XOR scheme to reconstruct a valid PNG file.**

## Solution:
The encryption had rotated every byte by its index and then XORed the data with an 8-byte repeating key. To reverse this, I first undid the rotation on just the first eight bytes so that the PNG header became partially visible again. Since a valid PNG header is fixed and known, I compared the rotated bytes against the standard header to extract the XOR key by simply XORing the two. Once the key was recovered, I applied it sliding across the entire file to undo the original XOR step. After that, I reversed the index-based rotation on all bytes to restore the true PNG data. Finally, because the PNG chunks were corrupted after decryption, I recalculated all CRC values for every chunk so the file became valid and could be opened normally. The final decrypted image was saved as final.png.
<br/>

**Python decryption code**
```ruby
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
**Recovered PNG:** 
<br/>
<img width="250" height="250" alt="quote_decrypted_fixed" src="https://github.com/user-attachments/assets/580c3c37-a8bc-4a03-81b7-81e69e4de9fd" />

## Flag:
```
 nite{to_b3_X0R_n0t_t0_b3333}
```

## Concepts learnt:
- I learned how index-based byte rotations can be reversed by applying the opposite rotation, and how known file signatures like the PNG header can be used to extract an XOR key simply by comparing expected bytes to encrypted ones. I also understood how sliding XOR encryption can be undone by applying the same key across the whole file in reverse, and how PNG files require correct CRC values for each chunk, which can be recomputed after decryption to restore the file to a valid state.

# 4. Willy’s Chocolate Experience

**The challenge required analyzing a custom modular-exponentiation scheme to recover a hidden exponent.**

## Solution:
The code that I have solved this challenge with transformed the flag into a large modulo-p number by evaluating an exponential function at the “ticket” integer.

How the encryption code worked: <br/>
It basically did the following: <br/>
It defined
    f(m) = 13^m + 37^m (mod p) <br/>
First, the flag bytes were converted into a large integer called ticket. Then the program computed a list containing:
    f(0), f(1), f(2), ..., f(ticket), <br/>
and finally kept only the last two values produced:
    f(ticket-1) and f(ticket). <br/>
These two outputs, called leftover, were the only information available. The challenge was to recover the hidden exponent ticket from just these two values. <br/>

Logic to reverse(decrypt) it : <br/>
Let the two leftover values be: <br/>
    X = f(ticket-1) and Y = f(ticket) <br/>
We note that:
    f(n) = 13^n + 37^n <br/>
    f(n-1) = 13^(n-1) + 37^(n-1) <br/>

The key observation is that dividing them gives a structured ratio involving (13/37)^(n-1). Using modular inverses, we compute: <br/>
    s = Y * X⁻¹ (mod p) <br/>
and rearrange the algebra to isolate: <br/>
    t = (13/37)^(n-1) (mod p) <br/>

Once we have this value, recovering n-1 becomes a discrete logarithm problem. Since p−1 factors into many small primes, a Pohlig–Hellman dlog solver can efficiently compute: <br/>
    k = log_r(t) where r = 13 * 37⁻¹ (mod p) <br/>
Thus:
    ticket = k + 1 <br/>

Finally, this recovered integer is converted back into bytes using long_to_bytes(ticket) to obtain the original flag. <br/>

I used GPT to assemble the number-theoretic logic and generate the working Python solver. <br/>
<br/>
**Python decryption code**
```ruby
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
- I learned how modular exponentiation with large primes can leak information when outputs are related, and how taking ratios of consecutive values reveals structure that can be simplified using modular inverses. This led to the idea of recovering the hidden exponent through a discrete logarithm. I also learned that when the modulus has a factorable order, the Pohlig–Hellman method can efficiently solve this discrete log, making the secret integer recoverable. Finally, I learned how to convert the recovered exponent back into bytes to reconstruct the original flag.
  ***


# 5. spAES Oddity

**The challenge required me to understanding the AES encryption and decrypt the flag using input/output sequence of the oracle.**

## Solution:
The service acted as an **AES encryption oracle**.
It accepted user-controlled input (in hex), appended the secret flag internally, and returned the AES-encrypted ciphertext.

However, there was a twist:
- The input must always be of odd length
- AES works on 16-byte blocks
- This odd-length constraint caused predictable alignment issues that could be abused

**How the encryption worked :**
- The server encrypted: [user_input || flag]
- AES was used in ECB-like behavior (same plaintext block→same ciphertext block)
- The oracle rejected even-length inputs, but this was bypassed by appending a dummy byte

  
**Core idea of the attack:**
The flag had to be recovered 2 bytes at a time(to check the even bytes too)
Then we carefully choose padding so that:
`[PAD][KNOWN_FLAG][UNKNOWN_2_BYTES] = exactly 16 bytes`

When two guessed bytes are **correct**, the resulting AES block matches the target block
Since AES is deterministic, block equality confirms correct guesses


**Step-by-step logic:**
<p align="center">
                                                            Start with the known prefix: `nite{`
<br/>                                                                      |
<br/>                         Add padding so that (padding + known_flag + 2 bytes) aligns perfectly to a 16-byte AES block
<br/>                                                                      |
<br/>                                   Send only padding to the oracle to capture the target ciphertext block
<br/>                                                                      |
<br/>                                                    Brute-force all possible character pairs 
<br/>                                                                      |
<br/>                                                               For each guess:
<br/>                                              1.  Send [padding + known_flag + guessed_pair]
<br/>                                      2.  Compare the resulting ciphertext block with the target block
<br/>                                                3.  If blocks match → guessed pair is correct
<br/>                                            4. Append the correct bytes and repeat until } is found
<br/>                                       5. The odd-length restriction was bypassed by appending a dummy byte

</p>

**Python solver script :**
```ruby
from pwn import *
import string

# --- Configuration ---
HOST = "spaesoddity.nitephase.live"  
PORT =  45673    
BLOCK_SIZE = 16

# Based on regex: [-_A-Da-z0-4] + }
charset = string.ascii_lowercase + "ABCD" + "0123456789" + "-_}"

def get_oracle_output(io, payload):
    """
    Sends payload to oracle.
    Handles the Odd-length constraint automatically by appending a dummy byte
    if we are in the 'verification' phase (sending full blocks).
    """
    # The payload we send MUST be odd length.
    # If we are sending a guess that fills a block (even), we append a dummy byte.
    msg = payload
    if len(msg) % 2 == 0:
        msg += b"A" 
        
    io.sendlineafter(b'input in hex:', msg.hex().encode())
    
    response = io.recvline().strip()
    if b"Major Tom" in response:
        return None
        
    return bytes.fromhex(response.decode())

def solve():
    io = remote(HOST, PORT)
    
    # We start with the known prefix
    flag = b"nite{"
    print(f"[+] Starting Attack. Known: {flag}")

    # We solve 2 bytes at a time
    while b"}" not in flag:
        
        # 1. Calculate Padding
        # We want [PAD][FLAG][c1][c2] to equal 16 bytes exactly.
        # len(pad) + len(flag) + 2 = 16
        pad_len = (16 - (len(flag) + 2) % 16) % 16
        
        # Verify our logic holds (Pad must be odd)
        if pad_len % 2 == 0:
            print("[-] Critical alignment error. This strategy requires Odd 'known' length.")
            break

        prefix = b"A" * pad_len
        
        # 2. Get Target Block
        # We send just the prefix. The server appends the flag.
        # Structure: [Prefix] [Flag_Known] [Unknown1] [Unknown2] | [Rest...]
        ciphertext = get_oracle_output(io, prefix)
        
        # Calculate which block contains our target
        # (pad + known + 2 bytes) is exactly a multiple of 16
        target_block_index = (len(prefix) + len(flag) + 2) // 16 - 1
        target_block = ciphertext[target_block_index*16 : (target_block_index+1)*16]

        print(f"[*] cracking next 2 bytes... (Pad: {pad_len}, Block: {target_block_index})")
        
        found_pair = False
        
        # 3. Brute Force Pair
        # Complexity: ~39 * 39 = 1521 requests (very fast)
        for c1 in charset:
            for c2 in charset:
                guess_chars = (c1 + c2).encode()
                
                # Construct the guess payload
                # Note: This payload is EXACTLY block-aligned (Even length).
                # The get_oracle_output function will append a dummy byte to make it Odd.
                guess_payload = prefix + flag + guess_chars
                
                guess_ct = get_oracle_output(io, guess_payload)
                
                # Extract the corresponding block
                guess_block = guess_ct[target_block_index*16 : (target_block_index+1)*16]
                
                if guess_block == target_block:
                    flag += guess_chars
                    print(f"[+] Found: {flag}")
                    found_pair = True
                    break
            
            if found_pair:
                break
        
        if not found_pair:
            print("[-] Failed to find pair. Check charset or alignment.")
            break

    print(f"\n[SUCCESS] Final Flag: {flag.decode()}")
    io.close()

if __name__ == "__main__":
    solve()
```

**Terminal working:** 

```
(pwntools) shraddhatiwari@LAPTOP-F2C51A3F:~/src$ nano aes.py
(pwntools) shraddhatiwari@LAPTOP-F2C51A3F:~/src$ python3 aes.py
[+] Opening connection to spaesoddity.nitephase.live on port 45673: Done
[+] Starting Attack. Known: b'nite{'
[*] cracking next 2 bytes... (Pad: 9, Block: 0)
[+] Found: b'nite{D4'
[*] cracking next 2 bytes... (Pad: 7, Block: 0)
[+] Found: b'nite{D4v1'
[*] cracking next 2 bytes... (Pad: 5, Block: 0)
[+] Found: b'nite{D4v1d_'
[*] cracking next 2 bytes... (Pad: 3, Block: 0)
[+] Found: b'nite{D4v1d_B0'
[*] cracking next 2 bytes... (Pad: 1, Block: 0)
[+] Found: b'nite{D4v1d_B0w1'
[*] cracking next 2 bytes... (Pad: 15, Block: 1)
[+] Found: b'nite{D4v1d_B0w13-'
[*] cracking next 2 bytes... (Pad: 13, Block: 1)
[+] Found: b'nite{D4v1d_B0w13-s_'
[*] cracking next 2 bytes... (Pad: 11, Block: 1)
[+] Found: b'nite{D4v1d_B0w13-s_0d'
[*] cracking next 2 bytes... (Pad: 9, Block: 1)
[+] Found: b'nite{D4v1d_B0w13-s_0dds'
[*] cracking next 2 bytes... (Pad: 7, Block: 1)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w'
[*] cracking next 2 bytes... (Pad: 5, Block: 1)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r'
[*] cracking next 2 bytes... (Pad: 3, Block: 1)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r3_'
[*] cracking next 2 bytes... (Pad: 1, Block: 1)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r3_n3'
[*] cracking next 2 bytes... (Pad: 15, Block: 2)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r3_n3v3'
[*] cracking next 2 bytes... (Pad: 13, Block: 2)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r3_n3v3r_'
[*] cracking next 2 bytes... (Pad: 11, Block: 2)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r3_n3v3r_1n'
[*] cracking next 2 bytes... (Pad: 9, Block: 2)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r3_n3v3r_1n_y'
[*] cracking next 2 bytes... (Pad: 7, Block: 2)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r3_n3v3r_1n_y0u'
[*] cracking next 2 bytes... (Pad: 5, Block: 2)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r3_n3v3r_1n_y0ur_'
[*] cracking next 2 bytes... (Pad: 3, Block: 2)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r3_n3v3r_1n_y0ur_f4'
[*] cracking next 2 bytes... (Pad: 1, Block: 2)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r3_n3v3r_1n_y0ur_f4v0'
[*] cracking next 2 bytes... (Pad: 15, Block: 3)
[+] Found: b'nite{D4v1d_B0w13-s_0dds_w3r3_n3v3r_1n_y0ur_f4v0r}'

[SUCCESS] Final Flag: nite{D4v1d_B0w13-s_0dds_w3r3_n3v3r_1n_y0ur_f4v0r}
[*] Closed connection to spaesoddity.nitephase.live port 45673

```

## Flag:
```
nite{D4v1d_B0w13-s_0dds_w3r3_n3v3r_1n_y0ur_f4v0r}
```

## Concepts learnt:
- AES is a deterministic block cipher: identical plaintext blocks produce identical ciphertext blocks
- How block alignment can be abused in encryption oracles
- Practical implementation of a byte-at-a-time ECB-style attack
- How input constraints (like odd-length restrictions) can often be           bypassed and exploited instead of blocking attacks

  ***
