---
title: "ByteBreach 2025.2 — Token 03: Trajectory_Uncertain"
date: 2026-01-02 00:00:00 +0100
categories: [CTF, Web]
tags: [bytebreach, javascript, devtools, console, analysis]
---

Token 03 writeup from **ByteBreach CTF 2025.2**. This challenge tests JavaScript analysis, DevTools discipline, and following broken resources to a remotely hosted script. All work was performed in an authorized CTF environment.

<!--more-->

> **Info**  
> Images are intentionally omitted until assets are uploaded, to keep GitHub Pages `htmlproofer` checks passing.
{: .prompt-info }

## Challenge Overview

- **Event:** ByteBreach CTF 2025.2
- **Category:** Web / JavaScript Analysis
- **Status:** Solved
- **Token:** `Trajectory_Uncertain`

After Token 2, we land on **“Shai-Hulud Supply Chain Attack”**. The page looks like a normal article and shows no token.

The key sentence states (paraphrased): the URL and token are shown via separate code. That is the signal to inspect JavaScript behavior.

## First Signal: “Loading…” That Never Completes

At the bottom, the page displays:

```text
Loading additional information...
```

…but no content ever appears.

> **Warning**  
> When a page claims it is loading something but nothing loads, open DevTools immediately and look for failing requests.
{: .prompt-warning }

## DevTools: Console Errors

In **Developer Tools → Console** we observe a failing request:

```text
/render.js
```

with:

```text
404 Not Found
```

The console also hints that a `remote_url` version might exist.

> **Important**  
> In CTFs, console errors are rarely accidental — they often point directly to the intended path.
{: .prompt-important }

## Discovering the Remote URL

The console output reveals a variable like:

```text
remote_url: https://drive.google.com/uc?export=download&id=...
```

This indicates the real logic is hosted externally. Opening `remote_url` directly downloads a JavaScript file (commonly named `render.js`).

## Inspecting the JavaScript

The script defines a function (e.g., `renderInfo()`) that:

- Locates an element by ID:
  ```text
  dynamic-content
  ```
- Injects HTML via `innerHTML`
- The injected HTML contains the token and next URL

A representative snippet is:

```html
<p><strong>Token 3: Trajectory_Uncertain</strong></p>
<p><strong>Next Token: https://github.com/BeyondMachines/secondary-case-for-sanity</strong></p>
```

> **Note**  
> The token is not hardcoded in the page HTML. It is injected only at runtime by JavaScript.
{: .prompt-info }

## Why the Token Was Invisible

Because `/render.js` fails to load, the render function never runs, and the dynamic container remains empty. The “broken” script reference is the challenge mechanism.

## Result

```text
Token 3: Trajectory_Uncertain
Next Token: https://github.com/BeyondMachines/secondary-case-for-sanity
```

## Notes

- Always check DevTools console first
- Broken JS loads can be intentional clues
- Reading small, unobfuscated JS pays off fast
