# 1. Quick Mistake

## Solution:


<img width="1920" height="1128" alt="Screenshot 2025-12-30 235201" src="https://github.com/user-attachments/assets/9982b614-dc1d-4d21-9562-9d0a59c602f1" />
<img width="1920" height="1128" alt="Screenshot 2025-12-31 213657" src="https://github.com/user-attachments/assets/38fc4461-853e-46e0-907b-8f65e6aaa9ce" />

Attacker SCID is 2457ce19cb87e0eb
Attacker: 192.0.2.66 (Reuses Legit Client's SCID).
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

***
