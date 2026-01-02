---
title: "ByteBreach 2025.2 — Token 01: Calibrated_Absence"
date: 2026-01-02 00:00:00 +0100
categories: [CTF, Cryptography]
tags: [bytebreach, cryptography, otp, vigenere]
---

Token 01 writeup from **ByteBreach CTF 2025.2**. This challenge is a classical alphabet-based one-time pad / Vigenère-style decryption using a known plaintext pad. All work was performed in an authorized CTF environment.

<!--more-->

> **Note**
>
> Images are intentionally not embedded yet.  
> This keeps GitHub Pages `htmlproofer` checks green until the assets are committed.
{: .prompt-tip }

## Challenge Overview

- **Event:** ByteBreach CTF 2025.2
- **Category:** Cryptography
- **Status:** Solved
- **Token:** `Calibrated_Absence`

The challenge redirected to a **CryptPad document** that contained an **encrypted password** plus hints pointing to a known plaintext pad.

## Observations and Hints

The description explicitly referenced:

- One-time pad encryption
- The first paragraph of *Anna Karenina*
- Translation by **Constance Garnett**

This strongly suggests a **known-plaintext OTP / Vigenère-style cipher**, where the “pad” is derived from a known text.

## Challenge Screenshot

![ByteBreach Token 01 - Challenge]({{ site.baseurl }}/assets/img/bytebreach/token1.png){: width="820" }

*Click to zoom:* open the image in a new tab if your browser blocks inline zoom.

---

## Given Data

**Encrypted password**
```text
OMSHCHTOXDCHHTVGMN
```

**Known plaintext (pad)**
```text
HAPPYFAMILIESAREALL
```

## Decryption Logic

Alphabet arithmetic:

- Map letters to numbers: `A=0, B=1, ..., Z=25`
- For each position:

```text
plaintext = (cipher - key) mod 26
```

`26` is used because the English alphabet has 26 letters.

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

**Output**
```text
HMDSECTCPSUDPTECMC
```

## Solved Screenshot

![ByteBreach Token 01 - Solved]({{ site.baseurl }}/assets/img/bytebreach/token1solved.png){: width="820" }

---

## Result

```text
Token 1: Calibrated_Absence
Next Token: http://walloftext.codeonion.net
```

## Notes

- Tokens are **case-sensitive**
- Literary references are common in crypto challenges
- Classic cipher fundamentals save time in CTFs
