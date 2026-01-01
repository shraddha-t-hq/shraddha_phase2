# 1. Symmetric Starter
> I just learnt how to encrypt my messages using a symmetric cipher.
> I hope no one can read it now.
**The challenge demanded that I analyze the custom keystream construction and exploit the leaked nonce bits to recover the AES key and decrypt the message.**

## Solution:
The encryption scheme uses `AES` in `ECB` mode to generate a keystream, which is then XORed with the plaintext. <br/>
However, the keystream generation depends on a nonce derived from the AES key itself. During each round, the most significant bit (`MSB`) of the nonce is leaked <br/>
and accumulated into a variable called shifts, which is written to the output file.

Since:
- the nonce is initialized directly from the AES key, and
- the MSB of the nonce is leaked for a large number of rounds,
- it becomes possible to reconstruct the key by modeling the nonce evolution symbolically.

To achieve this, I used the Z3 SMT solver to represent the AES key as symbolic variables and enforced constraints that matched the leaked MSB at every round.<br/>
Once all constraints were satisfied, Z3 produced a valid AES key. Using the recovered key, I reconstructed the keystream, decrypted the ciphertext,<br/>
and extracted the flag from the plaintext.


**Python decryption code**
```ruby
from Crypto.Cipher import AES
from Crypto.Util.number import bytes_to_long
from pwn import xor
from z3 import *
import re

# -----------------------------
# Load challenge output
# -----------------------------
with open("out.txt") as f:
    ct_hex = f.readline().strip()
    leak_hex = f.readline().strip()

ciphertext = bytes.fromhex(ct_hex)

ROUNDS = 128
leak_bits = list(map(int, bin(int(leak_hex, 16))[2:].zfill(ROUNDS)))

MOD = 1 << 128
ROT = 3

# -----------------------------
# Z3: recover key
# -----------------------------
solver = Solver()

key_bytes = [BitVec(f'k{i}', 8) for i in range(16)]
key = Concat(*key_bytes)
nonce = key
acc = BitVecVal(0, 128)

for i in range(ROUNDS):
    bit = leak_bits[i]

    # MSB leak constraint
    solver.add(Extract(127, 127, nonce) == bit)

    # accumulate leaked bits
    acc = (acc << 1) | bit

    # nonce update (same as chal)
    nonce = (nonce + acc) & (MOD - 1)
    nonce = RotateLeft(nonce, ROT)

assert solver.check() == sat
model = solver.model()

recovered_key = bytes(model[b].as_long() for b in key_bytes)
print("[+] Recovered key:", recovered_key.hex())

# -----------------------------
# Rebuild keystream
# -----------------------------
def rol(x, n):
    return ((x << n) | (x >> (128 - n))) & (MOD - 1)

def keystream(key):
    cipher = AES.new(key, AES.MODE_ECB)
    nonce = bytes_to_long(key)
    leak = ""

    while True:
        leak += str(nonce >> 127)
        nonce = (nonce + int(leak, 2)) & (MOD - 1)
        yield cipher.encrypt(nonce.to_bytes(16))
        nonce = rol(nonce, ROT)

# -----------------------------
# Decrypt
# -----------------------------
ks = keystream(recovered_key)
plaintext = b''.join(
    xor(ciphertext[i:i+16], next(ks))
    for i in range(0, len(ciphertext), 16)
)

# -----------------------------
# Extract flag
# -----------------------------
flag = re.search(rb'nite\{.*?\}', plaintext)
print("[+] Flag:", flag.group().decode())
```

**Terminal working:** 

```ruby
(pwntools) shraddhatiwari@LAPTOP-F2C51A3F:~/handout10$ python3 sym.py
[+] Recovered key: 0148a672548041e25c87ea397d3f5a70
[+] Flag: nite{wh00ps_l34k3d_2_mUch}
```

## Flag:
```
nite{wh00ps_l34k3d_2_mUch}
```
## Concepts learnt:
- I learned how partial information leakage, such as a single bit per round, can completely compromise cryptographic security.
- I gained hands-on experience using Z3 SMT solver to recover secret values by modeling cryptographic state transitions.
- I understood why custom cryptographic constructions are dangerous when not formally analyzed.
- I reinforced the importance of keeping nonce generation independent of secret keys.


# 2. stronk rabin
> rabin weak. I make it stronk. oh no. it broke.
**The challenge demanded that I exploit the Rabin decryption oracle to recover the modulus and reconstruct the original message.**

## Solution:
The challenge provides a DEC oracle for a Rabin cryptosystem. A known property of Rabin encryption is that a single ciphertext decrypts to multiple possible plaintexts
modulo N. Since the oracle returns one valid result per query, repeatedly querying the oracle with the same ciphertext allows us to collect all possible outputs it can produce.

By sending the same ciphertext to the DEC function multiple times, a set of unique integers is obtained. 
These values represent different combinations of Rabin plaintext roots `modulo N`. After collecting all possible outputs, they are sorted to analyze their structure.

In Rabin decryption, the largest and smallest values in this set correspond to `x (mod N)` and `-x (mod N)` respectively. 
Since `-x (mod N)` equals `N - x`, adding these two values directly reveals the `modulus N`. 
This allows recovery of the public modulus without any prior knowledge of the key.

Some of the values returned by the oracle correspond to `2 * m (mod N)`, which occurs due to how the Chinese Remainder Theorem combines square roots in Rabin cryptosystems.
To recover the original plaintext, each collected value is multiplied by the modular inverse of `2 modulo N`.

All resulting candidates are converted back into bytes and checked for the expected flag format. One of these plaintexts contains the correct flag, completing the challenge.
<br/>
**Python decryption code**
```ruby
from pwn import *
from Crypto.Util.number import *
import json

r = remote('localhost', 9999)

r.recvline()
c = json.loads(r.recvline().decode().strip())["C"]

possibles = set()

flags = set()
while len(flags) != 153:
    tosend = json.dumps({'func': 'DEC', 'args' : [c]})
    r.sendline(tosend.encode())
    uh = json.loads(r.recvline().decode())['retn']
    flags.add(int(uh))
    print(len(flags))

ok = sorted(list(flags))
N = ok[-1] + ok[1]
print(f"{N = }")

mults = [long_to_bytes((inverse(2, N) * i) % N) for i in ok]
for i in mults:
    if b'nite' in i:
        print(i)
```
*I used the python code in the solution repository to solve this.*
*The followig code also works:*
```
from pwn import remote
from Crypto.Util.number import inverse, long_to_bytes
import json

HOST, PORT = "localhost", 9999
TARGET_COUNT = 153

def get_ciphertext(conn):
    conn.recvline()
    data = json.loads(conn.recvline().decode())
    return int(data["C"])

def query_dec(conn, cipher):
    payload = json.dumps({
        "func": "DEC",
        "args": [cipher]
    })
    conn.sendline(payload.encode())
    response = json.loads(conn.recvline().decode())
    return int(response["retn"])

def recover_all_plaintexts(conn, cipher):
    results = set()
    while len(results) < TARGET_COUNT:
        val = query_dec(conn, cipher)
        results.add(val)
        print(len(results))
    return sorted(results)

def recover_modulus(values):
    return values[-1] + values[1]

def extract_flag(values, modulus):
    inv2 = inverse(2, modulus)
    for v in values:
        candidate = long_to_bytes((v * inv2) % modulus)
        if b"nite" in candidate:
            return candidate
    return None

def main():
    r = remote(HOST, PORT)

    cipher = get_ciphertext(r)
    all_vals = recover_all_plaintexts(r, cipher)

    N = recover_modulus(all_vals)
    print(f"Recovered modulus N = {N}")

    flag = extract_flag(all_vals, N)
    if flag:
        print(flag)

if __name__ == "__main__":
    main()
```
## Flag:
```
nite{str0nk_r4b1n_br34k_by_0r4cl3}
```

## Concepts learnt:
- I learned how Rabin encryption produces multiple valid plaintexts during decryption and why careless oracle design is dangerous.
- I understood how mathematical properties of modular arithmetic can be exploited to recover secret parameters such as the modulus.
- I gained experience abusing a decryption oracle to extract sensitive cryptographic information.
- I reinforced why cryptographic implementations must carefully control what information is exposed to users.
  
***
