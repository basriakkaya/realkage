---

title: "ByteBreach 2025.2 - Token 01: Calibrated\_Absence"

date: 2026-01-02

categories: \[CTF, Cryptography]

tags: \[bytebreach, cryptography, otp, vigenere]

math: true

---



Token 01 writeup from \*\*ByteBreach CTF 2025.2\*\*, focused on a classical one-time pad / Vigenere-style cryptography challenge. All actions were performed in an authorized CTF environment.



---



\## Challenge Overview



\- \*\*Event:\*\* ByteBreach CTF 2025.2

\- \*\*Category:\*\* Cryptography

\- \*\*Status:\*\* Solved

\- \*\*Token:\*\* Calibrated\_Absence



The challenge redirected to a \*\*CryptPad document\*\* containing an \*\*encrypted password\*\* and a set of subtle hints.



---



\## Challenge Screenshot



<p align="center">

&nbsp; <img src="/realkage/assets/img/token1.png"

&nbsp;      alt="ByteBreach Token 1 CryptPad Challenge"

&nbsp;      width="720">

</p>



<p align="center"><em>CryptPad page showing the encrypted password and OTP hint.</em></p>



---



\## Observations and Hints



The challenge description explicitly referenced:



\- One-time pad encryption

\- The first paragraph of \*Anna Karenina\*

\- Translation by \*\*Constance Garnett\*\*



These clues strongly suggest a \*\*known-plaintext OTP / Vigenere-style cipher\*\*, where the key is derived from a known literary text.



---



\## Given Data



\### Encrypted Password

`OMSHCHTOXDCHHTVGMN`



\### Known Plaintext (Pad)

`HAPPYFAMILIESAREALL`



This string corresponds to the opening line of \*Anna Karenina\* in the specified English translation.



---



\## Decryption Logic (Conceptual)



The cipher works using classical alphabet-based arithmetic:



1\. Each letter is mapped to a number ($A = 0, B = 1, ..., Z = 25$).

2\. The decryption formula for each character position is:



$$P = (C - K) \\pmod{26}$$



Where:

\- $P$ is the Plaintext character

\- $C$ is the Cipher character

\- $K$ is the Key (Pad) character

\- Modulo 26 ensures the result stays within the English alphabet.



---



\## Decryption Script (Python)



To automate the decryption, I used a simple Python script:



```python

import string



A = string.ascii\_uppercase

cipher = "OMSHCHTOXDCHHTVGMN"

pad = "HAPPYFAMILIESAREALL"



plain = "".join(

&nbsp; A\[(A.index(c) - A.index(k)) % 26]

&nbsp; for c, k in zip(cipher, pad)

)



print(f"Decrypted Flag: {plain}")
```

### Script Output



```text

Decrypted Flag: HMDSECTCPSUDPTECMC
Token 1: Calibrated\_Absence

```

\*\*Token 1:\*\* `Calibrated\_Absence`

\*\*Next Token URL:\*\* \[http://walloftext.codeonion.net](http://walloftext.codeonion.net)

