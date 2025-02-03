---
title: "Dekryptering af Forsvarets Efterretningstjenestes kryptochallenge (2023)"
excerpt: "Løsning af en krypteringsudfordring fra Forsvarets Efterretningstjeneste ved hjælp af RSA og stream cipher (Salsa20). Opgaven krævede analyse af kryptografiske hints og dekryptering af en skjult besked<br/><img src='/images/fe_krypto_artikel.jpg' width='500' height='300'>"
collection: portfolio
---


**Resume**

I denne rapport præsenteres løsningen på en krypteringsudfordring udgivet af **Forsvarets Efterretningstjeneste**. Opgaven bestod i at analysere og afkode en krypteret besked ved hjælp af RSA og en passende stream cipher. 

Løsningen involverede følgende trin:

 - Beregning af RSA-parametre (`N, e, d`) og udledning af en hemmelig nøgle (`key`)
- Validering af nøglen ved hjælp af opgavens betingelser `key mod 254 = 204`.
- Identifikation af en passende stream cipher (`Salsa20`), der understøtter en 256-bit nøgle og en 64-bit nonce.
- Dekryptering af beskeden ved at anvende `SHA-256` hash af nøglen som input til `Salsa20`.

Den endelige besked blev succesfuldt dekrypteret som:

> *Godt klaret! Hvis du har mod på mere, så send os en ansøgning. Vi glæder os til at høre fra dig.*

Rapporten dokumenterer processen, valg af algoritmer og de anvendte metoder i detaljer.

 ---
 **Forsvarets Efterretningstjeneste - Udfordringen**
 Forsvarets Efterretningstjeneste har i et jobopslag lavet en ny udfordring i kryptering. I jobopslaget ses blandt andet følgende billede ([https://www.fe-ddis.dk/da/karriere/krypto-profiler/](https://www.fe-ddis.dk/da/karriere/krypto-profiler/))
 
![FE-krypto-artikel](/images/fe_krypto_artikel.jpg)   

Endnu en ledetråd findes i artiklen:
```
c = 
d7a7e396df976cf6c59adce5d1381ea020a35af126efd0d5d380e4ccb74758e0f05e86a0bed
61ac75d5a1dfd029c8792ced99c5abf33354505e288f0b9bda280c9099506be3c3ee818b5e4
05e1fbf45903708cd067cafa34aa5f5b88958ae6603b4a427ab2
```
Ledetråden vil senere anvendes.
Ser man nærmere på billedet, ses en sætning øverst placeret, hvor dette er skrevet med almindelige bogstaver eller plaintekst, hvilket kunne tyde på en krypteret sætning:
```
Ymzj ul yri silx wfi yarvcg jrr yri Alczv efxcv yzekj
```
Her er der anvendt cæsarkryptering og afprøves forskellige rulninger, så får man dekrypteret sætningen til:
```
Hvis du har brug for hjælp så har Julie nogle hints.
```
Ud fra dette hint, så analyseres den artikel, FE har lavet, hvor man kan læse mere om Julies arbejde i FE's kryptoenhed ([https://www.fe-ddis.dk/da/karriere/krypto-profiler/](https://www.fe-ddis.dk/da/karriere/krypto-profiler/)).

I denne artikel fås endnu et hint:
```
SHZpcyBkdSBzZXIgdGFsbGV0IHNvbSBldCBwb2x5bm9taXVtIGthbiBmb3JtbGVuIHheMTI5ICs
geV4xNSA9ICh4XjQzICsgeV41KSh4Xjg2IC0geV41ICogeF40MyArIHleMTApIG3lc2tlIGJydW
dlcz8=
```
Dette er en Base64-kodet streg og ved at dekode den, så får man følgende output:
```
Hvis du ser tallet som et polynomium kan formlen 
x^129 + y^15 = (x^43 + y^5)(x^86 - y^5 * x^43 + y^10) måske bruges?
```

Dette hænger sammen med baggrunden på billedet fra artiklen, hvor der yderligere bemærkes følgende mønstre:

- `N = 322^129 + 1333^15`
- `e = 2^16 + 1`
- `key % 0xfe = 204`
- `key = 1337^d mod N`
- `m = decrypt(sha256("%d" % key), c)`
- `rsa`
- `stream`
- `0x00*8`
- `count = 0`

Det som der først bemærkes, er, at der nævnes `RSA` og der fremgår `N, e` og `key`. Det tyder klart på, at udfordringen handler om RSA-kryptering, hvor man får givet `N` og `e`, sådan at man har det offentlige nøglepar `(N, e)`.

Her bruges det givne polynomium, hvor venstresiden svarer til `N = 322^129 + 1333^15`, sådan at man kan aflæse `x = 322` og `y = 1333`.

Når der arbejdes med RSA-kryptering, så kan man bruge at `N` kan opskrives som produktet `N = p * q`, hvor `p` og `q` er primtal. 

Derfor bruges polynomiet igen, hvor faktoriseringen svarer til hhv. `p` og `q`. 
Det betyder at `p = x^43 + y^5` og `q = x^86 - y^5 * x^43 + y^10`.

Ved at bruge `Python`, så regnes værdierne af `p` og `q`, hvorfra `N` kan bestemmes. 

 Dertil kan det bruges at `e = 2^16 + 1`, da Eulers totientfunktion \\[\phi(N) = (p - 1)(q-1)]\\, hvorfra man har at 
 \\[d \cdot e \equiv 1 \mod \phi(N)]\\, hvilket leder til at \\[d \equiv e^{-1} \mod \phi(N)]\\.
 
 Ved brug af `Python` opnås:
 
 **INDSÆT KODEBLOK**
 
 Og tilhørende output:
 
 **INDSÆT OUTPUT**

Dermed er det offentlige nøglepar `(N, e)` fundet.

Det er nu muligt at finde `key`, da det er givet at `key = 1337^d mod N`. Ved at bestemme `key`, så er det muligt at tjekke betingelsen `key % 0xfe = 204`, hvor `0xfe` henviser til hexadecimal, hvilket i decimal svarer til 254, altså `key % 254 = 204`.

Ved at eksekvere følgende kode i `Python`:


**INDSÆT KODE**


Så fås:


**INDSÆT OUTPUT**


---

Dermed er de første hints fra billedet om RSA-krypteringen brugt. 
Nu er der følgende tilbage:

- `m = decrypt(sha256("%d" % key), c)`
- `stream`
- `0x00*8`
- `count = 0`

Denne del tog en del tid at løse, da jeg manglede viden om området. Den matematiske del var relativ simpel qua min matematiske baggrund. Men ved følgende logik blev brugt til at analysere dette:

- Man har en besked `m`, som er den dekrypterede version af cipherteksten `c`.
- Cipherteksten `c` kan dekrypteres ved at bruge en nøgle, der er afled af en `SHA-256` hashværdi.
- Hashværdien genereres som `sha256("%d" % key)`, hvor `key` indsættes i en formaterbar streng.

Det betyder at hvis man skaber en `SHA256` hashværdi på `d mod key`, så kan det dekrypteres ved at bruge cipherteksten. Men hvordan - det skal stadig undersøges. Og det er her de andre hints vil være brugbare.

Første skridt i processen er at konvertere cipherteksten, `c`, fra hexadecimal til bytes, da dekrypteringsalgoritmer arbejder med bytearrays og ikke direkte med hex-strenge. Herefter genereres en nøgle ved hjælp af `SHA-256`, som tager en inputværdi, konverterer den til bytes, og beregner en hashværdi med en fast længde på 256 bit. Denne hashværdi bruges som nøgle til dekryptering.

Dette gøres i `Python` ved at bruge `hashlib`-biblioteket:

**INDSÆT KODEBLOK**

Det giver en værdi af `key_hash` i bytes, som:
```
b'y\x80\xa9z\x11\xe2\xb9\x966\x13;\x9b\xaf\x92-\xe2\x86\xd3\xcf\x898CS\x7f%Z\xb0\xafV\x98)\x85'
```
Det skal dekrypteres ved at bruge cipherteksten `c`. Men det er her jeg sad fast.
For hvad betyder følgende:
- `stream`
- `0x00*8`
- `count = 0`

Ved at søge på `"stream" encryption` på Google, så findes resultater om stream-ciphers, der er er en teknik som arbejder bit for bit og transformerer tekstbidder om til kode som ikke kan læses, medmindre man har den passende nøgle. Den nøgle er fundet, så nu skal det afgøres hvilken stream-cipher der skal arbejdes med.

Når man ser på `0x00*8`, så bemærkes det at `0x00` er en enkelt byte med alle bits lig med 0. Dem er der 8 af og en enkelt byte har 8 bits, hvilket giver 64 bits.

Derudover arbejdes der med `SHA-256`, hvilket har en længde på 256 bit. Ligeledes kunne `count = 0` antyde at den stream-cipher man arbejder med skal initialiseres. 

Ved at bruge Google Dorking og søge på `"stream" "256" "64"`, så er første hit til siden [Cryptography Stack Exchange](https://crypto.stackexchange.com/questions/87946/salsa20-expands-a-256-bit-key-and-a-64-bit-nonce-into-a-270-byte-stream-where), hvor der skrives om en stream-cipher kaldet `Salsa20`. Derudover har [Wikipedia](https://en.wikipedia.org/wiki/Stream_cipher#Comparison) en beskrivelse af stream-ciphers, hvor en tabel viser forskellige ciphers og kategorierne:
- Stream cipher 
- Creation date 
- Speed (cycles per byte) (bits) 
- Attack
- Effective key-length 
- Initialization vector 
- Internal state 
- Best known 
- Computational complexity

Leder man efter en cipher der indeholder en nøglelængde på 256, håndterer 64 bits og skal initialiseres, så finder man igen `Salsa20`. Læser man om denne stream-cipher, så kan man se at den bruger en nøglelængde på 256 bits, hvilket stemmer overens med det specificerede krav. Den interne tilstand består af en 64-bit nonce kombineret med en 64-bit stream position, hvilket svarer til en samlet tilstand, hvor 64 bits anvendes iterativt til at holde styr på positionen i nøglestrømmen. `Salsa20` anvender en iterativ metode til generering af nøglestrømmen baseret på runder af beregninger, hvilket også opfylder kravet om en iterativ tilgang.

Ved at bruge `Python` og biblioteket `Crypto.Cipher`, så kan `Salsa20` implementeres. Det gør man ved at bruge en nonce, som er `0x00*8` med som konverteret i byte giver `b'x00'*8`. Derudover skal den have nøgles angivet i byte, hvilket er `key_hash`.

`Salsa20` kan dermed initialiseres ved følgende kode:


**INDSÆT KODEBLOK**


Og dekrypteringen kan dermed eksekveres ved følgende:


**INDSÆT KODEBLOK**


Dette resulterer i, at den oprindelige besked nu er dekrypteret og udfordringen fra **Forsvarets Efterretningstjeneste** er løst:

**INDSÆT OUTPUT**
<blockquote style="color: red; background-color: lightgray; padding: 10px; border-left: 5px solid red;">
    Dette er en farvet blockquote.
</blockquote>


\\[
m = c^d \mod N
\\]

