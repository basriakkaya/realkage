---

title: "ByteBreach 2025.2 — Token 05: Relativistic\_Records"

date: 2026-01-02 19:15:00 +0100

categories: \[CTF, OSINT]

tags: \[bytebreach, email, zip, password, pdf, drive, ripmime, 7zip]

---



\## Token 05 — Relativistic\_Records



This stage is the direct continuation of the \*\*Terraform → Google Drive → E-mail (.eml)\*\* chain established in the previous tokens.  

The objective is to correctly identify the downloaded artifact, extract its contents, and recover the next token from a \*\*password-protected ZIP archive\*\*.



---



\## 1. Downloading the Drive Artifact



The `next\_token\_url` obtained from the Terraform variables pointed to a Google Drive file.  

After downloading, the file type was immediately inspected:



```bash

gdown --fuzzy "https://drive.google.com/file/d/<FILE\_ID>/view" -O next.bin

file next.bin

```



Output:



```text

SMTP mail, ASCII text

```



This indicates that the Drive artifact is not a PDF or ZIP, but an \*\*e-mail message (`.eml`)\*\*.



---



\## 2. Extracting E-mail Attachments



The file was renamed and processed to extract its attachments:



```bash

mv next.bin next.eml

ripmime -i next.eml -d out

ls -la out

```



Extracted artifacts:



\- `details.zip` — password protected

\- `Ivie Clayton CV.pdf`

\- HTML and plaintext e-mail body files



The e-mail body explicitly mentions that the attached material is password protected.



---



\## 3. Identifying the ZIP Password



Attempting to extract `details.zip` with standard tools resulted in a password prompt.  

Instead of brute-forcing, an OSINT-driven approach was used.



Relevant hints found in the CV:



\- \*\*Boston Marathon 2019 — Bib #1421\*\*

\- A recurring persona-related keyword: \*\*Pixelate\*\*



Combining these contextual clues leads to the correct password:



```text

Pixelate1421

```



Extraction was performed using 7-Zip for reliability:



```bash

7z t -p"Pixelate1421" out/details.zip

7z x -y -p"Pixelate1421" -oextracted out/details.zip

```



---



\## 4. Recovering the Token



The ZIP archive contains a single file, `details.txt`:



```bash

cat extracted/details.txt

```



Contents:



```text

Token 5: Relativistic\_Records

Next Token: https://sites.google.com/view/yellowstone-transit-log/

```



---



\## Result



\- \*\*Token 05:\*\* `Relativistic\_Records`

\- \*\*Next Stage:\*\* A Google Sites page hosting the following challenge artifact



This token demonstrates the importance of correct file-type identification and narrative-based password derivation in chained CTF challenges.



