---
title: "Dekryptering af Forsvarets Efterretningstjenestes kryptochallenge (2023)"
excerpt: "Løsning af en krypteringsudfordring fra Forsvarets Efterretningstjeneste ved hjælp af RSA og stream cipher (Salsa20). Opgaven krævede analyse af kryptografiske hints og dekryptering af en skjult besked<br/><img src='/images/fe_krypto_artikel.jpg' width='500' height='300'>"
collection: portfolio
---


Tekst her
```python
print("Hello World!")
```

Ny kodeblok:

<div class="code-container">
    <div class="code-header">
        <div class="circle red"></div>
        <div class="circle yellow"></div>
        <div class="circle green"></div>
    </div>
    <pre><code class="language-python">
import hashlib

from Crypto.Cipher import Salsa20

# Givet data fra udfordringen
x = 322
y = 1333

# Beregn p, q, N og phi(N)
p = pow(x, 43) + pow(y, 5)
q = pow(x, 86) - pow(y, 5) * pow(x, 43) + pow(y, 10)
N = p * q
e = pow(2,16)+1
phi_n = (p - 1) * (q - 1)
d = pow(e, -1, phi_n)
    </code></pre>
</div>



En anden blok:

<div class="code-container">
    <div class="code-header">
        <div class="circle red"></div>
        <div class="circle yellow"></div>
        <div class="circle green"></div>
    </div>
    <pre><code class="language-python">
print("Hello World!") </code></pre>
</div>
