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
    <pre class="language-python"><code class="language-python">print("Hello World!")</code></pre>
</div>

Test med billede:

<img src="/images/code_example_fe.png" alt="En kodeblok" width="500">


## RSA og matematisk notation

RSA-kryptering bygger på eksponentiation modulo et stort primtalprodukt.  
Den offentlige nøgle består af et modulus \\( N \\) og en eksponent \\( e \\), og kryptering af en besked \\( m \\) sker som:

\\[
c = m^e \mod N
\\]

For at dekryptere bruger vi den private nøgle \\( d \\), der opfylder:

\\[
m = c^d \mod N
\\]

