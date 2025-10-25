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

