---
title: "ByteBreach 2025.2 — Token 04: Requiem_for_Velocity"
date: 2026-01-02 00:00:00 +0100
categories: [CTF, Cloud]
tags: [bytebreach, osint, terraform, cloud, misconfiguration]
---

Token 04 writeup from **ByteBreach CTF 2025.2**. This is a classic OSINT pivot into a cloud misconfiguration pattern (Terraform variables exposed). All work was performed in an authorized CTF environment.

<!--more-->

> **Info**  
> Images are intentionally omitted until assets are uploaded, to avoid GitHub Pages `htmlproofer` failures.
{: .prompt-info }

## Challenge Overview

- **Event:** ByteBreach CTF 2025.2
- **Category:** Cloud Security / OSINT / Code Review
- **Status:** Solved
- **Token:** `Requiem_for_Velocity`

After Token 3, we are pointed to a GitHub repository:

```text
https://github.com/BeyondMachines/secondary-case-for-sanity
```

At first glance it looks abandoned — that is the point. The repository is an OSINT breadcrumb.

## Recon: GitHub Observations

Two signals stand out:

1. **README.md**
   - Mentions the project is abandoned
   - Claims “latest code” is hosted on a CodeOnion subdomain

2. **Repository “About”**
   - Described as: “SecondarySanity site”

> **Warning**  
> In CTFs, “abandoned repo” usually means the repo is not the solution — it is a pointer to a live target.
{: .prompt-warning }

## Building the Target URL

From earlier tokens we know the base domain:

```text
codeonion.net
```

From the repo text we derive the subdomain:

```text
secondarysanity
```

Combined:

```text
http://secondarysanity.codeonion.net
```

## Visiting the Site: Terraform Red Flag

The page contains a developer-style message that references Terraform deletion, including **Terraform variables**.

> **Important**  
> Mentioning Terraform variables is a high-signal hint. In CTFs this often implies a publicly accessible variables file (e.g., `terraform.tfvars`).
{: .prompt-important }

## Critical Step: Guessing the Variables File

In Terraform projects, sensitive variables are often stored in:

```text
terraform.tfvars
```

So the direct high-confidence check is:

```text
http://secondarysanity.codeonion.net/terraform.tfvars
```

> **Note**  
> This is not blind brute forcing. It is a standard filename guess based on explicit hints and common Terraform practice.
{: .prompt-info }

## Token Extraction

Inside the exposed `terraform.tfvars` file:

```text
Token 4: Requiem_for_Velocity
```

And the next URL appears as a variable, e.g.:

```text
next_token_url = "https://drive.google.com/..."
```

## Key Takeaways

- GitHub repos can be OSINT pivots, not the target itself
- Terraform references often point to `terraform.tfvars`
- Cloud CTFs frequently leak configuration rather than app bugs
- Reading the hint carefully is faster than scanning
