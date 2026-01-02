---
title: "ByteBreach 2025.2 — Token 02: Threshold_of_Antiquity"
date: 2026-01-02 00:00:00 +0100
categories: [CTF, OSINT]
tags: [bytebreach, osint, html, source-code, s3]
---

Token 02 writeup from **ByteBreach CTF 2025.2**. This is an OSINT + attention challenge: the “wall of text” is a distraction, and the real instruction is hidden in the page source. All work was performed in an authorized CTF environment.

<!--more-->

> **Info**  
> This post avoids embedding images until the assets are uploaded, to prevent GitHub Pages `htmlproofer` failures.
{: .prompt-info }

## Challenge Overview

- **Event:** ByteBreach CTF 2025.2
- **Category:** OSINT / Web (source inspection)
- **Status:** Solved
- **Token:** `Threshold_of_Antiquity`

After Token 1, the next page is effectively a **long, multilingual distraction**. The core hint is to inspect the **HTML source**.

## Initial Observations

The source contained obvious decoys, for example:

```html
<meta flag="{not_the_real_one}">
flag{fake_flag_123}
```

> **Warning**  
> Anything that is explicitly labeled as “fake” / “not real” is an intentional decoy. Move on immediately.
{: .prompt-warning }

## Hidden Content and CSS Trick

Hidden elements existed in the DOM:

```html
<span class="hidden">...</span>
```

And the CSS made them effectively invisible:

```css
.hidden {
  color: #1a1a1a;
}
```

## The Real Clue (Actionable Instruction)

One paragraph provided clear operational guidance:

- Storage: **AWS S3**
- Region: **eu-central-1**
- Bucket:
  ```text
  cicadoidea6699
  ```
- File:
  ```text
  configuration.txt
  ```
- Constraint: **“but in Macedonian Cyrillic”**

> **Important**  
> This is the only actionable instruction on the page. Everything else is noise.
{: .prompt-important }

## Translating the Filename

“configuration” in Macedonian Cyrillic:

```text
конфигурација
```

Final filename:

```text
конфигурација.txt
```

## Accessing the File (Public S3)

Two equivalent URL formats work:

```text
https://s3.eu-central-1.amazonaws.com/cicadoidea6699/конфигурација.txt
```

```text
https://cicadoidea6699.s3.eu-central-1.amazonaws.com/конфигурација.txt
```

> **Tip**  
> No exploitation required — this is a pure OSINT / observation challenge.
{: .prompt-tip }

## Result

```text
Token 2: Threshold_of_Antiquity
Next Token: http://shai-hulud.yieldcat.com
```

## Notes

- Always inspect page source
- “Wall of text” pages typically hide a single precise instruction
- Apply language constraints literally (here: Cyrillic filename)
