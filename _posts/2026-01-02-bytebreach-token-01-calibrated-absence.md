---
title: "ByteBreach 2025.2 – Token 01: Calibrated Absence"
date: 2026-01-02 12:00:00 +0100
categories: [CTF, Cryptography]
tags: [bytebreach, cryptography, otp, vigenere]
---

Token 01 writeup from **ByteBreach CTF 2025.2**, focused on a classical  
**one-time pad / Vigenère-style cryptography challenge**.

All actions were performed in an **authorized CTF environment**.

---

## Challenge Overview

- **Event:** ByteBreach CTF 2025.2  
- **Category:** Cryptography  
- **Status:** Solved  
- **Token:** `Calibrated_Absence`

The challenge redirected to a **CryptPad document** containing an  
**encrypted password** and multiple cryptographic hints.

---

## Challenge Screenshot

![ByteBreach Token 1 CryptPad Challenge](/assets/img/token1.png)

*CryptPad page showing the encrypted password and the OTP hint.*

---

## Observations and Hints

The challenge description explicitly referenced:

- One-time pad encryption  
- The first paragraph of *Anna Karenina*  
- Translation by **Constance Garnett**

These clues strongly indicate a  
**known-plaintext OTP / Vigenère-style cipher**,  
where the key is derived from a known literary text.

---

## Given Data

### Encrypted Password

```
OMSHCHTOXDCHHTVGMN
```

### Known Plaintext (Pad)

```
HAPPYFAMILIESAREALL
```

This string corresponds to the opening line of *Anna Karenina*  
in the specified English translation.

---

## Decryption Logic

The cipher uses classical alphabet-based arithmetic:

- Each letter is mapped to a numeric value  
  (`A = 0, B = 1, ..., Z = 25`)
- Decryption formula for each position:

```
plaintext = (cipher - key) mod 26
```

- Modulo **26** ensures the output stays within the English alphabet.

This works because the English alphabet has exactly 26 characters.

---

## Decryption Script (Python)

```python
import string

A = string.ascii_uppercase
cipher = "OMSHCHTOXDCHHTVGMN"
pad = "HAPPYFAMILIESAREALL"

plain = "".join(
    A[(A.index(c) - A.index(k)) % 26]
    for c, k in zip(cipher, pad)
)

print(plain)
```

### Script Output

```
HMDSECTCPSUDPTECMC
```

---

## Result

```
Token 01: Calibrated_Absence
Next Token URL: http://walloftext.codeonion.net
```

---

## Notes

- Tokens are **case-sensitive**
- Literary references are common in cryptography challenges
- Strong fundamentals in classical ciphers save significant time in CTFs
