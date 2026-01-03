# 1. Quick Mistake
**The challenge demanded that I analyze a PCAP file, identify the correct protocol stream, reconstruct a transferred payload, and correctly interpret file signatures to recover the flag.**

## Solution:
Firstly, I opened the given `PCAP` file in Wireshark and analyzed the traffic using the Protocol Hierarchy. This revealed the presence of `QUIC` traffic running over `UDP`.
First step to consruct the flag was to find the attacker IP and ID. This is where i found it : 
<br/>
<img width="1920" height="1128" alt="Screenshot 2025-12-30 235201" src="https://github.com/user-attachments/assets/9982b614-dc1d-4d21-9562-9d0a59c602f1" />

Attacker SCID was `2457ce19cb87e0eb`<br/>
Attacker: `192.0.2.66` 
<br/>
Instead of following the `UDP` stream, I followed the `QUIC` stream, as QUIC reconstructs application-layer data correctly.<br/>
Upon switching the `QUIC` stream view to RAW, I noticed the hexadecimal sequence: `1f 8b 08`<br/>
These bytes correspond to the magic header of a `GZIP` file, indicating that the attacker had transferred a gzip-compressed payload in raw form. 
<br/>
<img width="1920" height="1128" alt="Screenshot 2025-12-31 213657" src="https://github.com/user-attachments/assets/38fc4461-853e-46e0-907b-8f65e6aaa9ce" />

**Reconstructing the Payload**<br/>
I copied the raw hexadecimal data starting from the gzip header and saved it into a file named `payload.hex`. Since the data was in hexadecimal form, I converted it back to binary using `xxd`.
I then verified the file type.

**Extracting the Archive**<br/>
After decompression, the resulting file turned out to be a `POSIX tar archive`, not plain text. I extracted the archive contents.

**Identifying the Encryption Key**<br/>
Inspecting the `.env` file revealed an AES key stored as an environment variable. This strongly indicated that the flag was AES-encrypted inside the application logic.

**Decrypting the Flag**<br/>
Using the  AES key from .env, I used a small decryption script (decrypt.py) to recover the plaintext flag.

Then I combined all required findings to get the actual flag.

**Terminal working:** 
```ruby
shraddhatiwari@LAPTOP-F2C51A3F:~$ nano payload.hex
shraddhatiwari@LAPTOP-F2C51A3F:~$ xxd -r -p payload.hex payload.gz
shraddhatiwari@LAPTOP-F2C51A3F:~$ file payload.gz
payload.gz: gzip compressed data, last modified: Sun Dec  7 12:19:10 2025, max compression, original size modulo 2^32 10240
shraddhatiwari@LAPTOP-F2C51A3F:~$ gunzip payload.gz

gzip: payload.gz: decompression OK, trailing garbage ignored
shraddhatiwari@LAPTOP-F2C51A3F:~$ file payload
payload: POSIX tar archive
shraddhatiwari@LAPTOP-F2C51A3F:~$ tar -xvf payload
.env
app.py
requirements.txt
README.md
shraddhatiwari@LAPTOP-F2C51A3F:~$ cat .env
AES_FLAG_KEY=wEN64tLF1PtOglz3Oorl7su8_GQzmlU2jbFP70cFz7c=
DEBUG=True
SECRET_KEY=dev_secret_123shraddhatiwari@LAPTOP-F2C51A3F:~$ nano decrypt.py
shraddhatiwari@LAPTOP-F2C51A3F:~$ python3 decrypt.py
qu1c_d4t4gr4m_pwn3d}
```

## Flag:
```
nite{192.0.2.66_2457ce19cb87e0eb_qu1c_d4t4gr4m_pwn3d}
```

## Concepts learnt:
- How to properly analyze `QUIC` traffic.
- Understood the importance of magic bytes (`1f 8b 08`) in identifying compressed data.
- Learned how to reconstruct files from hexadecimal network data using `xxd`.
- Gained experience handling gzip trailing garbage caused by network transmission.
- Learned how attackers may mistakenly leak entire application source code over the network.
- Applied AES decryption using extracted environment variables to recover sensitive data.
***
