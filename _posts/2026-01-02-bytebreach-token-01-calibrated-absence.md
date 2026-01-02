---
title: "ByteBreach 2025.2 - Token 01: Calibrated_Absence"
date: 2026-01-02 12:00:00 +0100
categories: [CTF, Cryptography]
tags: [bytebreach, beyondmachines, otp, vigenere]
---

Token 01 writeup from **ByteBreach CTF 2025.2**, focused on a classical
OTP / Vigenère-style cryptography task. All actions were performed in an
authorized CTF environment.

---

## Challenge Overview

- **Event:** ByteBreach CTF 2025.2
- **Organizer:** BeyondMachines
- **Category:** Cryptography
- **Status:** Solved
- **Token:** `Calibrated_Absence`

The challenge redirected to a **CryptPad document** with an encrypted password
and a hint pointing to a one-time pad derived from a known text.

---

## Challenge Screenshot

![ByteBreach Token 1 CryptPad Challenge](/realkage/assets/img/token1.png){: width="720" }
_Challenge page showing the encrypted password and OTP hint._

---

## Observations and Hints

The description referenced:

- One-time pad encryption
- The first paragraph of *Anna Karenina*
- Translation by **Constance Garnett**

This strongly suggests a **known-plaintext OTP / Vigenère-style** approach where
the pad/key is derived from a known literary line.

---

## Given Data

### Encrypted password

```text
OMSHCHTOXDCHHTVGMN
```

### Known plaintext (pad)

```text
HAPPYFAMILIESAREALL
```

---

## Decryption Logic (Conceptual)

We map letters to numbers (`A = 0` ... `Z = 25`) and decrypt position-by-position:

```text
plaintext = (cipher - key) mod 26
```

We use **mod 26** because the English alphabet has 26 letters.

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

### Output

```text
HMDSECTCPSUDPTECMC
```

---

## Solved Screenshot

![ByteBreach Token 1 Solved](/realkage/assets/img/token1solved.png){: width="720" }
_Decrypted password accepted by the challenge._

---

## Result

```text
Token 1: Calibrated_Absence
Next Token URL: http://walloftext.codeonion.net
```

---

## Notes

- Tokens are **case-sensitive**
- Literary references often act as pads in CTF crypto challenges
- Recognizing classical ciphers quickly saves time in competitions
