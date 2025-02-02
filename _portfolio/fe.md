---
title: "Dekryptering af Forsvarets Efterretningstjenestes kryptochallenge (2023)"
excerpt: "Løsning af en krypteringsudfordring fra Forsvarets Efterretningstjeneste ved hjælp af RSA og stream cipher (Salsa20). Opgaven krævede analyse af kryptografiske hints og dekryptering af en skjult besked<br/><img src='/images/fe_krypto_artikel.jpg' width='500' height='300'>"
collection: portfolio
---


**Resume**  
I denne rapport præsenteres løsningen på en krypteringsudfordring udgivet af  
Forsvarets Efterretningstjeneste.  
Opgaven bestod i at analysere og afkode en krypteret besked ved hjælp af RSA og en passende stream cipher.  

**Løsningen involverede følgende trin:**  
- Beregning af RSA-parametre (*N*, *e*, og *d*) og udledning af en hemmelig nøgle (*key*).  
- Validering af nøglen ved hjælp af opgavens betingelser (*key mod 254 = 204*).  
- Identifikation af en passende stream cipher (*Salsa20*), der understøtter en 256-bit nøgle og en 64-bit nonce.  
- Dekryptering af beskeden ved at anvende en SHA-256-hash af nøglen som input til *Salsa20*.  

Den endelige besked blev succesfuldt dekrypteret som:  
> *"Godt klaret! Hvis du har mod på mere, så send os en ansøgning. Vi glæder os til at høre fra dig."*  

Rapporten dokumenterer processen, valg af algoritmer og de anvendte metoder i detaljer.  

---  

### Forsvarets Efterretningstjeneste - Udfordringen  
Forsvarets Efterretningstjeneste har i et jobopslag lavet en ny udfordring i kryptering. I jobopslaget ses blandt andet følgende billede:  

![FE-krypto-artikel](/images/fe_krypto_artikel.jpg)  
[Kilde](https://www.fe-ddis.dk/da/karriere/krypto-profiler/)  

### Ledetråd fra artiklen  

```plaintext
c = 
d7a7e396df976cf6c59adce5d1381ea020a35af126efd0d5d380e4ccb74758e0f05e86a0bed
61ac75d5a1dfd029c8792ced99c5abf33354505e288f0b9bda280c9099506be3c3ee818b5e4
05e1fbf45903708cd067cafa34aa5f5b88958ae6603b4a427ab2
```

### Cæsar-kryptering af skjult tekst  
Teksten fundet i billedet:  

```
Ymzj ul yri silx wfi yarvcg jrr yri Alczv efxcv yzekj
```

Dekrypteres til:  

```
Hvis du har brug for hjælp så har Julie nogle hints.
```

### Analyse af hintet  
- Hintet leder til en artikel om Julies arbejde i FE's kryptoenhed:  
  [FE krypto-profil](https://www.fe-ddis.dk/da/karriere/mod-en-krypto-profil/)  
- I denne artikel findes endnu et hint:  

```plaintext
SHZpcyBkdSBzZXIgdGFsbGV0IHNvbSBldCBwb2x5bm9taXVtIGthbiBmb3JtbGVuIHheMTI5ICs
eV4xNSA9ICh4XjQzICsgeV41KSh4Xjg2IC0geV41ICogeF40MyArIHleMTApIG3lc2tlIGJydW
```

- Dette er en Base64-kodet streng. Ved at dekode den fås:  

```
Hvis du ser tallet som et polynomium kan formlen
x^129 + y^15 = (x^43 + y^5)(x^86 - y^5 * x^43 + y^10) måske bruges?
```

### Beregning af RSA-parametre  

```python
x = 322
y = 1333
p = x**43 + y**5
q = x**86 - y**5 * x**43 + y**10
N = p * q
e = 2**16 + 1
d = pow(e, -1, (p-1)*(q-1))
```

- Dette giver følgende værdier:  
  - **p** = 688339168571559064510...
  - **q** = 473810810989785206183...
  - **N** = 326142539696924869207...
  - **d** = 186348681530906948019...

- Nu kan *key* beregnes:  

```python
key = pow(1337, d, N)
key % 254  # Tjek af betingelse
```

- Dette verificerer, at *key* er korrekt.  

### Dekryptering med Salsa20  
- Den givne ciphertext *c* dekrypteres ved hjælp af SHA-256 og *Salsa20*:  

```python
from Crypto.Cipher import Salsa20
import hashlib

key_hash = hashlib.sha256(str(key).encode()).digest()
cipher = Salsa20.new(key=key_hash, nonce=b'\x00'*8)
decrypted_msg = cipher.decrypt(bytes.fromhex(c))
```

- Dette giver:  

```plaintext
Dekrypteret besked (Salsa20): Godt klaret! Hvis du har mod på mere, så send os en ansøgning. Vi glæder os til at høre fra dig.
```

---  

### Det fulde Python-script  

```python
# Python script til dekryptering
# [Her kan du indsætte hele koden]
```

---  
Dette viser løsningen på krypteringsudfordringen fra FE ved hjælp af RSA, SHA-256 og Salsa20.

<blockquote style="color: red; background-color: lightgray; padding: 10px; border-left: 5px solid red;">
    Dette er en farvet blockquote.
</blockquote>


\\[
m = c^d \mod N
\\]

