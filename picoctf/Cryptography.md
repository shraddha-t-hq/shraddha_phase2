# 1. Custom encryption

> Can you get sense of this code file and write the function that will decode the given encrypted file content. Find the encrypted file here flag_info and code file might be good to analyze and get the flag.
<br/> **> The code demanded that I decrypt the encrypted flag by providing a code file with the encryption process.**


## Solution:
It was required for me to write a code to reverse the encryption, I used gpt to do it.
<br/>
**source code** :
```
#!/usr/bin/env python3
# decode_flag.py
# Usage: python3 decode_flag.py
# Reverses the encryption described in the provided code file and prints the plaintext.

from typing import List

# Given values from flag_info / code
a = 90
b = 26
p = 97
g = 31
text_key = "trudeau"
cipher = [61578, 109472, 437888, 6842, 0, 20526, 129998, 526834, 478940, 287364, 0, 567886, 143682, 34210, 465256, 0, 150524, 588412, 6842, 424204, 164208, 184734, 41052, 41052, 116314, 41052, 177892, 348942, 218944, 335258, 177892, 47894, 82104, 116314]

def compute_shared_key(g: int, a: int, b: int, p: int) -> int:
    """
    Compute the Diffie-Hellman shared key (g^(a*b) mod p) in a safe way.
    Uses pow with modular exponentiation.
    """
    # faster / safer: compute v = g^b mod p, then key = v^a mod p
    v = pow(g, b, p)
    key = pow(v, a, p)
    return key

def recover_semi_cipher(cipher_list: List[int], shared_key: int) -> str:
    """
    From the integer cipher list recover the semi_cipher string where
    each integer was calculated as: ord(char) * shared_key * 311
    """
    denom = shared_key * 311
    chars = []
    for idx, c in enumerate(cipher_list):
        # Expect exact division. If denom is zero or c is zero, handle carefully.
        if denom == 0:
            raise ValueError("Denominator (shared_key * 311) is zero, can't decode.")
        if c == 0:
            # zero maps to ord 0 -> NUL char; but in practice might be a real 0 in ciphertext
            ord_val = 0
        else:
            if c % denom != 0:
                # Not exact division — warn but attempt int division (shouldn't happen in correct data)
                ord_val = c // denom
                print(f"Warning: cipher[{idx}] not divisible exactly by {denom}. Using floor division.")
            else:
                ord_val = c // denom
        # convert ord to character
        chars.append(chr(ord_val))
    return "".join(chars)

def undo_dynamic_xor(semi_cipher: str, text_key: str) -> str:
    """
    The original dynamic_xor_encrypt did:
      for i, char in enumerate(plaintext[::-1]):
          key_char = text_key[i % key_length]
          encrypted_char = chr(ord(char) ^ ord(key_char))
          cipher_text += encrypted_char
    So semi_cipher[i] = plaintext[::-1][i] XOR key[i % key_len]
    To recover plaintext:
      plaintext[::-1][i] = semi_cipher[i] XOR key[i % key_len]
      then reverse to get plaintext.
    """
    key_len = len(text_key)
    recovered_rev_chars = []
    for i, ch in enumerate(semi_cipher):
        key_char = text_key[i % key_len]
        orig_ord = ord(ch) ^ ord(key_char)
        recovered_rev_chars.append(chr(orig_ord))
    plaintext_reversed = "".join(recovered_rev_chars)
    plaintext = plaintext_reversed[::-1]
    return plaintext

def main():
    shared_key = compute_shared_key(g, a, b, p)
    print(f"[+] Computed shared_key = {shared_key}")

    semi = recover_semi_cipher(cipher, shared_key)
    print(f"[+] Recovered semi_cipher (raw chars): {repr(semi)}")

    plaintext = undo_dynamic_xor(semi, text_key)
    print("\nDECODED PLAINTEXT")
    print(plaintext)
    
if __name__ == "__main__":
    main()
```
## Flag:
```

```
## Concepts learnt:


## Notes:

## Resources:


# 2. miniRSA

> Let's decrypt this: [ciphertext]? Something seems a bit small.
<br/> **>This challenge demanded that I perform calculations to decrypt the encrypted ciphertext.**


## Solution:
The ciphertext given (`c`) was relatively smaller than the `N` value. The `e` (e=3) value was also very small compared to the N value. This reduced the RSA encryption equation to : `m = cuberoot(c)`.This decrypted message was received in the form of a decimal number, which had to be converted into hexadecimal and then into ASCII to get the flag.

I used a python code which I found online on **Stack Overflow** in order to find the cuberoot of such a large number(c).
<br/>
**source code in python to calculate cuberoot** :

```
def find_invpow(x, n):
    high = 1
    while high ** n <= x:
        high *= 2
    low = high // 2 

    while low < high:
        mid = (low + high) // 2
        if low < mid and mid ** n < x:
            low = mid
        elif high > mid and mid ** n > x:
            high = mid
        else:
            return mid
    return mid + 1


print(find_invpow(2205316413931134031074603746928247799030155221252519872650073010782049179856976080512716237308882294226369300412719995904064931819531456392957957122459640736424089744772221933500860936331459280832211445548332429338572369823704784625368933,3))

```
**Converting hexadecimal equivalent to ASCII:** 
<br/>
<img width="1920" height="824" alt="Screenshot 2025-10-26 174059" src="https://github.com/user-attachments/assets/258e24e2-f658-49ed-9666-49fce6ee95a0" />

## Flag:
```
picoCTF{n33d_a_IArg3r_e_ccaa7776}
```
## Concepts learnt:
 I learnt about the special RSA case: where the `e` value is very small, the equation simplifies to a format in which we can decrypt a ciphertext by only having 3 parameters.
 <br/>
 The RSA encryption formula gives answers in decimal which first needs to be converted to hexadecimal and then to ASCII to get the human-readable form of the message.

## Resources:
>[https://stackoverflow.com/questions/356090/how-to-compute-the-nth-root-of-a-very-big-integer]
>[https://www.rapidtables.com/convert/number/decimal-to-hex.html]
>[https://neapay.com/online-tools/hex-to-ascii-converter.html]

# 3. rsa_oracle

> Can you abuse the oracle?
 An attacker was able to intercept communications between a bank and a fintech company. They managed to get the message (ciphertext) and the password that was used to encrypt the message.
Additional details will be available after launching your challenge instance.
<br/> **> This challenge demanded that I perform calculations(using python script) to decrypt the encrypted password first, and then decrypt the ciphertext.**


## Solution:
Firstly, I logged into the picoCTF portal using the `nc` linking. Then I used GPT to write out an integrated code that would call the pico portal and also find the password in its decrypted form. The following is the logic of the code to find out the decrypted password :
>we firtst encrypt the number 2
>now we have 2^e, we multiply by m^e 
>we decrypt 2^e*m^e, which will yield 2*m
>we grab the response, convert it from hexadecimal and divide by 2

<br/>
*I used jupyter notebook because pwn tools were not working on my system*
**source code in python** :

```
from pwn import *

connection = remote('titan.picoctf.net', 52263)

response = connection.recvuntil('decrypt.')
print(response.decode())
payload = b'E' + b'\n'

connection.send(payload)

response = connection.recvuntil('keysize):')
print(response.decode())

#We want to encrypt the number 2
payload = b'\x02' + b'\n'
connection.send(payload)
response = connection.recvuntil('ciphertext (m ^ e mod n)')
response = connection.recvline()

num=int(response.decode())*2336150584734702647514724021470643922433811330098144930425575029773908475892259185520495303353109615046654428965662643241365308392679139063000973730368839


response = connection.recvuntil('decrypt.')
print(response.decode())
payload = b'D' + b'\n'
connection.send(payload)

response = connection.recvuntil('decrypt:')
print(response.decode())
connection.send(str(num)+'\n')

response = connection.recvuntil('hex (c ^ d mod n):')
print(response.decode())
response = connection.recvline()
print(response.decode())

num=int(response,16)//2
print(hex(num))

hex_string=hex(num)[2:] # get rid of 0x
byte_array=bytes.fromhex(hex_string)
print(byte_array.decode('ascii'))

connection.close()

```
**Output :** 
<br/>
```
[x] Opening connection to titan.picoctf.net on port 52263
[x] Opening connection to titan.picoctf.net on port 52263: Trying 3.139.174.234
[+] Opening connection to titan.picoctf.net on port 52263: Done


C:\Users\SHRADDHA\AppData\Local\Temp\ipykernel_31820\184103291.py:6: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('decrypt.')


*****************************************
****************THE ORACLE***************
*****************************************
what should we do for you? 
E --> encrypt D --> decrypt.


C:\Users\SHRADDHA\AppData\Local\Temp\ipykernel_31820\184103291.py:12: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('keysize):')



enter text to encrypt (encoded length must be less than keysize):


C:\Users\SHRADDHA\AppData\Local\Temp\ipykernel_31820\184103291.py:18: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('ciphertext (m ^ e mod n)')
C:\Users\SHRADDHA\AppData\Local\Temp\ipykernel_31820\184103291.py:25: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('decrypt.')
C:\Users\SHRADDHA\AppData\Local\Temp\ipykernel_31820\184103291.py:31: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('decrypt:')


what should we do for you? 
E --> encrypt D --> decrypt.
 
Enter text to decrypt:


C:\Users\SHRADDHA\AppData\Local\Temp\ipykernel_31820\184103291.py:33: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  connection.send(str(num)+'\n')
C:\Users\SHRADDHA\AppData\Local\Temp\ipykernel_31820\184103291.py:35: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('hex (c ^ d mod n):')


decrypted ciphertext as hex (c ^ d mod n):
 6c60cc6a60

0x3630663530
60f50
[*] Closed connection to titan.picoctf.net port 52263
```

Then following the hints and using my terminal I decrypted the ciphertext by using the openssl portal.
**Terminal working :**
```
shraddhatiwari@LAPTOP-F2C51A3F:~$ openssl enc -aes-256-cbc -d -in secret.enc -k 60f50
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
picoCTF{su((3ss_(r@ck1ng_r3@_60f50766}
```
## Flag:
```
picoCTF{su((3ss_(r@ck1ng_r3@_60f50766}
```
## Concepts learnt:
 > **RSA malleability and oracle abuse** — I learned how an encryption oracle can be abused i.e. by encrypting a small known value (2), multiplying its ciphertext with the intercepted ciphertext, and asking the oracle to decrypt the product, you can recover a predictable relationship (2·m) and use it to derive the original secret. This demonstrates RSA’s vulnerability to chosen-ciphertext style manipulation when an oracle is available. This logic I learnt using a youtube video.
>**pwn tools** - I learnt to make effective use of pwn tools in CTFs in solving remote challenges. 
 <br/>

